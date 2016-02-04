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
