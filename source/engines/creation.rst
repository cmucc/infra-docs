Summoning a Daemon Engine
================================================================================

The process of creating a daemon engine is not that involved.

It requires the following steps:

1. Install Libvirt, virt-install, Kerberos, etc.
2. Get Kerberos host key with kadmin
3. Configure udev rule to grant world-access to /dev/kvm
4. Set up Hurricane Electric tunnel in /etc/network/interfaces
5. Set up nat-bridge with system-wide libvirt
6. Set up forward-bridge in /etc/network/interfaces
7. Configure qemu-bridge-helper to allow access to the two bridges in /etc/qemu/bridge.conf
8. Insert some configuration files into /etc/skel so new user accounts are set up properly

TODO be more detailed.
