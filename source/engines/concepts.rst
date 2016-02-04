Basic Concepts of the Daemon Engines
================================================================================

For a philosophical justification, look at :doc:`/philosophy/virtualization`.
Here we just describe the technologies in play in the daemon engines.

A daemon engine is much like a traditional shell server.
A traditional shell server allows someone to log in,
run some software,
and interactively get some work done.
Indeed, that is their primary purpose.

A daemon engine allows this too.
Users can log in,
run the same software,
and get work done just as well,
and indeed they're perfectly welcome to do so.
But this is not the purpose of the daemon engine.
The purpose of the daemon engine is to allow users to easily start long-running processes.
These are daemonized processes, in most cases.

There are many kinds of long-running processes that are useful.
Our specific goal is that
running any of a set of four kinds of programs
should not require any special privileges on daemon engines,
and should in general be well supported.
That is,
non-root users should be able to freely run these kinds of programs on daemon engines.

These kinds of programs are:

- Interactive network applications, such as ``irssi``
- Generic network daemons, such as ``znc``
- Virtual machines, such as QEMU; see :doc:`virtualization`
- Containers, such as ``systemd-nspawn``; see :doc:`containers`

(The first two can already be easily run without privileges.)

Other features of the daemon engines make running long-running processes easy and useful:

- Local home directories by default rather than AFS; see :doc:`local_storage`

- Additional network storage in large quantities by request; see :doc:`network_storage`

- Availability of the Nix package manager for handling dependencies of programs;
  see :doc:`nix`.
