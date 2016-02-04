================================================================================
Getting Admin Privileges
================================================================================

So, someone is joining the illustrious ranks
of the Computer Club administrators?
Here's how to give them the privileges appropriate to their station.
(More prosaically: This gives someone admin privileges over Kerberos and AFS.)

On all KDCs:

.. code-block:: sh

   sensible-editor /etc/heimdal-kdc/kadmind.acl
   # find the line for contribkey@CLUB.CC.CMU.EDU
   # add the following two lines above it
   # $NEWUSER/admin@CLUB.CC.CMU.EDU all *@CLUB.CC.CMU.EDU
   # $NEWUSER/admin@CLUB.CC.CMU.EDU all */*@CLUB.CC.CMU.EDU

On all KDCs and fileservers
(make sure you modify the AFS ``server/UserList`` on **all** of them!):

.. code-block:: sh

   sensible-editor /etc/openafs/server/UserList

On **any one** machine with an afs client, as admin, run:

.. code-block:: sh

   pts cu $NEWUSER.admin 
   pts adduser $NEWUSER.admin system:administrators

To actually log in as root on our machines,
the new admin should be added to ``/afs/club/service/etc/passwd.admin``.
Also, they should be added to the LDAP "wheel" group.
