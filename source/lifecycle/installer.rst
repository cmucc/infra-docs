==========================================================================
Installing an Operating System
==========================================================================

You need to actually get Debian (or whatever) onto the disk of your machine!
How?
One of these methods!

Make sure when in the Debian installer,
that you select to install an ssh server, and not a desktop environment...

PXE (for both virtual and physical machines)
=====

This is the nicest option as it works with both virtual and physical machines.
Note that it does require that you specify a (correct) MAC address in NetReg
(because PXE is configured over DHCP).

However:
- Our PXE images don't support Debian Jessie
- Our PXE images are inflexible, only allowing one Debian version to be installed
- PXE won't work for VMs behind NAT/only using an IPv6 address

Hit a certain (machine-dependent) keystroke displayed on screen while the machine is booting up to use PXE with physical machines.

For virtual machines, pass ``--pxe`` to ``virt-install``.
Unfortunately PXE might not actually be working for VMs for some reason...

Load distro installer directly in VM
=============================

``virt-install --location`` someURL

This will download the installer from the internet;
basically doing a network install.
This is second-best after PXE for VMs.

See the ``virt-install`` manpage for the URLs in general;
Debian's is ``http://www.club.cc.cmu.edu/pub/debian/dists/stable/main/installer-amd64/``.

You'll also need to pass ``--extra-args '--- console=ttyS0'``.

So:

``--location http://www.club.cc.cmu.edu/pub/debian/dists/stable/main/installer-amd64/``

ISO directly to VM
========

``virt-install --cdrom`` isoPath

Should be self-explanatory.


ISO on physical CD
===

Obviously only applies to physical machines.

We have some CDs, and a CD burner.
Download an ISO, burn it to the CD, and stick it in a physical machine!

ISO on physical USB
=====

Obviously only applies to physical machines.

We have lots of USB sticks, and plenty of machines to write to them from.
Download an ISO, dd it to the USB stick, and stick it in a physical machine!
