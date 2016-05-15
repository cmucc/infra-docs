==========================================================================
Hosting Club Servers
==========================================================================

A machine has to actually exist somewhere.
But where?

Daemon engine
=============

This is your best bet for VMs.
Make sure you're familiar with the concepts behind the daemon engines;
see :doc:`/core_systems/daemon_engines/index`.

Then, what you need to do is either pick an existing user account to create the VM under,
or create a new user account on some daemon engine to start the VM.
You can read about that in :doc:`/core_systems/daemon_engines/accounts`.

Physical box
=============

If there are spare physical machines,
you could use one of those.
But we generally use physical machines as some kind of VM host,
and just create VMs on top.

KVM machine
=============

You can put it on a more traditional KVM machine,
that isn't designed to be used as a daemon engine,
and can just host a VM boring-ly.

Nah, that's boring, forget it!
