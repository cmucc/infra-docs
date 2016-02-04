========================================
Remote access (serial console)
========================================

In general you need to make sure ``console=ttyS0`` is on the kernel command line.

With a VM you can ensure this as long as you installed with
``virt-install --location someurl --extra-args '--- console=ttyS0'``.

Not sure how this is set up for physical machines.

TODO Remote access, console or SSH access


