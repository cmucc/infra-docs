==========================================================================
Giving Permissions to Access Things
==========================================================================

Our fundamental tool for controlling permissions to administrate servers is the
``.k5login`` file, which can be located in any user's home directory.
This file lists Kerberos principals that are allowed to log in as that user.
(Of course, it only works if Kerberos is installed on the host!)

On daemon engines,
we use this ``.k5login`` file
to control access to special-purpose user accounts
used to run VMs for one specific aspect of club services.
This allows us to make permission granular.

The ``/root/.k5login`` file, which naturally allows people to log in as root,
can be auto-populated in several ways.
On LDAP machines,
the membership of the group "wheel" is dumped and put in that file.
On rsync machines,
everyone in ``/etc/passwd.admin`` (and ``/etc/root-k5login.local``?)
is put in that file.
