Daemon Engines
==========================================================================

.. For a philosophical justification, look at :doc:`/philosophy/virtualization`.
.. Here we just describe the technologies in play in the daemon engines.

Daemon engines are bare-metal servers which:

- Like shell servers, support *interactive usage*
- Unlike shell servers, are well-suited for *running daemons*,
  particularly virtual machines.

Daemon engines are intended to be used by **unprivileged users**.
Daemon engines rely on traditional Unix user isolation for security.

Examples of unprivileged daemons which should run on daemon engines
include, but are not limited to:

- Interactive network applications, such as ``irssi``
- Generic network daemons, such as ``znc`` or ``weechat`` relays
- Virtual machines, such as QEMU; see :doc:`virtualization`
- **Not Yet Implemented**:
  Containers, such as ``systemd-nspawn``; see :doc:`containers`

(The first two can already be easily run without privileges.)

Daemon engines differ from normal clubified servers in only a few ways:

- They support unprivileged virtualization.
- They have IPv6 connectivity provided by a Hurricane Electric tunnel
  so that we can automatically allocate IPs for VMs.
- Home directories are local instead of in AFS,
  for performance reasons and to avoid ticket expiration
  issues with daemons.
  Note that AFS otherwise functions normally on daemon engines.
- **Not Yet Implemented**:
  They have the Nix package manager installed, to allow unprivileged users
  to install programs efficiently without affecting other users
- **Not Yet Implemented**:
  Some people have suggested having some sort of fast (i.e. not AFS)
  network storage.
