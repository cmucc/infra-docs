==========================================================================
Account System Concepts
==========================================================================

So there's Kerberos and AFS and passwd and LDAP.


So!

We use Kerberos, AFS, rsync'd ``/etc/passwd``, LDAP, and probably more.

Kerberos is a mechanism to provide the abstract capability to authenticate to things.
When a Kerberos principal is created, someone gets the password to that principal,
and is able to authenticate (verifying that they are who they say they are)
as that principal. (with ``kinit``)
This, by default, gives them nothing (okay, you're that principal, who cares?),
but other services are configured
to recognize certain Kerberos principals and give them certain privileges.

The two chief services that recognize Kerberos principals are AFS and PAM.

When you are authenticated to the appropriate Kerberos principal,
you can then get access to your AFS space. (with ``aklog``)
So you can, you know, edit and create and delete files.
Keep in mind that Kerberos principals aren't directly represented in AFS.
Instead, a Kerberos principal allows you to authenticate yourself as controlling a PTS id,
the AFS idea of a "user account".

Likewise, 
when you are authenticated to the appropriate Kerberos principal,
you can log into our servers with ``ssh``.
If your SSH configuration is correct,
it should be able to authenticate you as who you say you are.
This goes through the PAM (Pluggable Authentication Modules) stack
on our systems, which also supports password-based and public-key-based authentication.
Again, Kerberos principals aren't directly represented as accounts on our servers.
Instead, PAM is configured to let Kerberos principals that match the
name of accounts on our servers, access those accounts.

For that to work, of course,
the appropriate accounts have to actually exist on each server.
AFS can handle synchronizing its concept of user accounts (PTS ids) just fine,
but there's no built-in way in Linux to synchronize user accounts across servers.
User accounts are, in the single-server configuration, found in ``/etc/passwd``
on each machine.
Traditionally, we've just rsync'd ``/etc/passwd`` around to all of our servers,
the ``rsync`` being triggered by a cron job.
That's a bit horrifying,
so an LDAP server was set up,
which replaces account lookups in ``/etc/passwd`` with network requests to the LDAP server.
Now some servers use ``rsync``, and some use LDAP...
