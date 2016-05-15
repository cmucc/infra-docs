================================================================================
Virtualization
================================================================================

Running a virtual machine without privileges is actually easy.
The QEMU hardware emulator can run just fine without any privileges or kernel APIs,
and it can even emulate a network stack.
This will provide a fully functional virtual machine.
However, this is very slow.

To be useful in production, QEMU needs two things.
QEMU needs to be able to use the Linux KVM API to accelerate its emulation,
which is accessed by opening ``/dev/kvm`` read/write.
And QEMU needs to be able to use some form of bridged networking set up by the kernel.

Fortunately, it is safe to make ``/dev/kvm`` world-read/writable.
(Or at least some distros, such as Fedora, believe so.)
And QEMU now comes with a small setuid binary called
``qemu-bridge-helper`` which can be configured
to set up a bridged networking for an unprivileged QEMU process.
So we can run a useful virtual machine just fine.

libvirt
=======================

Libvirt is a suite of tools that makes running virtual machines easier.
We're using it with QEMU/KVM.
Specifically, we're using the "QEMU user session" mode of libvirt, to run libvirt without privileges.

The XML configuration for the VM is in ``~/.config/libvirt/qemu/``.
It can be retrieved with ``virsh dumpxml $VM_NAME``.
Disk images are in ``~/.local/libvirt/images/``.

The primary entry point command for manipulating existing VMs is ``virsh``.
Just read the manpage.

For creating new VMs, you use ``virt-install``.
See :doc:`network`, :doc:`/machine_lifecycle/installer`, and the manpage.

Here's an example invocation, sans network:

.. code-block:: sh

   virt-install \
     --name vmname --description description \
     --memory 512 --disk size=10 \
     --extra-args '--- console=ttyS0' --graphics none \
     --location http://ftp.us.debian.org/debian/dists/stable/main/installer-amd64/


Inspecting VM filesystem
========================

Use ``guestmount``.

Remote access with libvirt
============================

Libvirt has nice remote access features.

Run this command to generate the libvirt URI you need to use for remote access:

.. code-block:: sh

   echo qemu+ssh://$USER@$HOSTNAME.club.cc.cmu.edu/session?socket=/run/user/$UID/libvirt/libvirt-sock

Let's break it down a little:

- ``qemu+ssh://`` indicates you want to use ssh to remotely access a QEMU VM host.
- ``$USER@$HOSTNAME.club.cc.cmu.edu``
  should be exactly what you typed after ``ssh`` to log in to this server in the first place.
- ``/session``
  specifies that you want to use the "user session" variant of QEMU/libvirt virtualization,
  rather than system-wide VMs only accessible to root.
- ``?socket=/run/user/$UID/libvirt/libvirt-sock``
  explicitly specifies how to talk to the long-running libvirt daemon;
  this is a just-in-case workaround for possible bugs.

For remote access with =virsh=, use that URI with:

.. code-block:: sh

   virsh -c 'thaturi' list

There's some configuration you can do to change the default libvirt URI, or alias them,
so you don't have to specify that long URI every time.
Check the manpage.

For the virt-manager GUI, use that URI with:

.. code-block:: sh

   virt-manager --connect='thaturi'

After you run virt-manager manually like this once, the URI will be saved so you can just run it normally afterwards.

Running QEMU directly
=======================

You can actually run QEMU directly just fine,
but the bookkeeping around it becomes a big pain,
so it's better to have libvirt handle it for production.

But nevertheless, the following command (or an appropriate variation thereof) will work:

   .. code-block:: sh

      qemu-system-x86_64 -enable-kvm \
        -kernel /boot/vmlinuz-3.16.0-4-amd64 \
	-initrd /boot/initrd.img-3.16.0-4-amd64 \
	-net nic -net bridge,br=virbr0 \
	-append 'console=ttyS0 break' -nographic -m 512

This will just drop you in the initrd.
If you want an actual system,
grab a live image from here_,
and provide the path to it as the last argument.
Make sure to also remove "break" from the -append argument,
that's what makes the system fall back to initrd.

.. _here: http://cdimage.debian.org/debian-cd/current-live/amd64/iso-hybrid/
