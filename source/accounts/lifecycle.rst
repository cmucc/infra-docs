================================================================================
Account Lifecycle
================================================================================

Here is described the birth, renewal, and perhaps death of club accounts.

Account Creation
================
A huge lot of things need to happen to create a club account.

Luckily, we have an account creation script,
located in ``/afs/club.cc.cmu.edu/system/scripts/perl/NewAcct.pl``.

Some things you should ensure before running it:

- kinit will blow away your permissions to your home directory,
  so make the accounts file in /tmp or some similar place.
- You must be in the admin `UserList` on all AFS servers
  (copper, silver, zinc, sodium, potassium, barium)
- You must have /usr/sbin in your PATH to run kadmin
- You should endeavour to do this on ``conch.club.cc.cmu.edu``, the admin shell

.. code-block:: sh

   kinit you/admin
   
   echo $SHORTNAME:$PASSWORD:$REALNAME:foo > accounts
   
   /afs/club.cc.cmu.edu/system/scripts/perl/NewAcct.pl accounts
   # pray that everything works

Accounts are added to LDAP (and thus are usable from LDAP-enabled machines) immediately.
All other machines will receive the new accounts upon synchronization
at the top of each hour.
'''If you are particularly impatient,'''
you can log onto these machines and manually run the synchronization script,
``/afs/club.cc.cmu.edu/system/scripts/sh/passwd-update.sh``.

Account Renewal
================

Accounts are created with a 2-year expiration time.
Every two years an account will receive mail suggesting it be renewed.
Here's how:

.. code-block:: sh

   ssh <you>@miscellany
   cd /var/sibyl
   kinit -S kadmin/admin <you>/admin
   export PATH=$PATH:/usr/sbin
   ./bin/renew_exp.rb foo{,/mail}
   kdestroy

Alternatively, manually:

* kinit you/admin
* kadmin
* get <user>*
* behold all their principals
* for each principal: modify --expiration-time=<newdate> <principal>


Account Destruction
====================

This is fairly uncommon, but occasionally we've botched account creation, or someone asks us to get rid of an account.  Here's how to clean things up.

Log in as administrator

Remove the afs mount point.  Note the dot before club.
 
.. code-block:: sh

   fs rmmount /afs/.club.cc.cmu.edu/usr/somebody

Remove the afs volumes.  This should remove the backup volumes too.

.. code-block:: sh

   vos remove -id mail.somebody
   vos remove -id user.somebody


Remove the pts entries

.. code-block:: sh

   pts delete somebody
   pts delete somebody.mail


Remove the user from ``/afs/club.cc.cmu.edu/service/etc/passwd.user``

Remove the user from LDAP.

Remove the file from ``/afs/club.cc.cmu.edu/service/mail/mailtabs/``

Delete the Kerberos principals.  Make sure to delete both somebody and somebody/mail.
