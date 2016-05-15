==========================================================================
Naming Club Servers
==========================================================================

Computer Club naming policy is maybe a bit complicated and we don't actually follow it.
Feel free to just give whatever hostname you want.

This url can be useful: http://namingschemes.com/

Core concepts
=============

- Each machine has a unique "cute" name as its hostname;
  the type of cute name is determined by the service the machine provides.
  Avoid resuing these names for new machines.
  Example: A machine called "whelk".
- For each machine, there is a DNS A record with that "cute" name pointing at the IP of that machine.
  This record should only be changed if the machine itself changes IP address.
  Example: An A record "whelk.club.cc.cmu.edu" pointing at IP address 128.237.157.1.
- For each machine, there is a DNS CNAME record with a "functional" name, pointing to the DNS A record.
  These functional names are tied to the service, rather than the machine, and are frequently reused.
  Example: A CNAME record "shell01.club.cc.cmu.edu" pointing at "whelk.club.cc.cmu.edu".

To refer to a machine's service (for example, in a load-balancer or public documentation) use the functional name, the CNAME.
For example, say "You can log in to any of shell0[123].club.cc.cmu.edu to use IRC".

To refer to a specific machine/hardware/operating system, use the cute name, the hostname, or the A record.
For example, say "whelk really needs to be upgraded from Wheezy".

Avoid reusing the "cute" names of machines;
when making a new machine that provides the same service as an old machine,
'''never''' use the same name as the machine currently providing that service.
(Only reuse a name after the machine it referred to has been dead for months or years and is purged from docs.)

Picking names
==============

How do you pick cute names and functional names?
It depends on the service. Follow this table.

Try and avoid having CNAMEs/functional names that point to machines that are dead or don't exist.

If you're making a new kind of service, add a row to this table.

==============  ================================================= =====================================================
Service         ''Machines providing this have a "cute" name like Instances of the service have a functional CNAME like
==============  ================================================= =====================================================
Shell machines  Shelled creatures (whelk, oyster, clam)           shell## (shell01, shell02, shell03)
--------------  ------------------------------------------------- -----------------------------------------------------
Kerberos KDCs   Dog breeds (labrador, collie)                     (specified by protocol) kerberos(-#)? (kerberos, kerberos-1, kerberos-2)
==============  ================================================= =====================================================

Example
==========

"labrador" is the hostname of a machine that serves as a KDC.

"labrador.club.cc.cmu.edu" is an A record that points to that machine.

"kerberos-2.club.cc.cmu.edu" is a CNAME record that points to "labrador.club.cc.cmu.edu".

Our Kerberos configuration refers to "kerberos-2.club.cc.cmu.edu" (along with the other KDCs).
