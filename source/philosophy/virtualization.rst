A Nice Way to Let Many Users Administer Virtual Machines
==========================================================================

Let's start with an overview of operating system fundamentals.
On the hardware that makes up a computer, there runs an operating system.
On top of the operating system, other programs can run, separated into processes.
The operating system abstraction allows access to hardware resources through a simpler interface;
This feature of operating systems called processes allows many different programs to run at once on the same hardware.
The "process" feature separates and isolates each program from each other, preventing them from interfering with each other's operation,
except when they need to use common resources such as hardware, or need to communicate or cooperate.

In modern times, we've also developed something called "virtual machines".
A virtual machine is a program which runs as a process on an operating system, just like any other program.
The computer hardware and operating system that the virtual machine process is running on is often called the "host";
e.g. "This server is a virtual machine host".
A virtual machine program simulates hardware, including a virtual CPU and virtual disk,
which allows an operating system to run on top of that virtual machine.
Then, on top of the operating system running inside the virtual machine,
we can run more processes.
The processes running inside the virtual machine are isolated from each other by the operating system inside the virtual machine.
As well, all of the processes running inside the virtual machine,
the operating system running inside the virtual machine, and the virtual machine program itself,
are isolated from other processes running on the host by the host operating system.

Now, here's a review question for you.
You might find it strange that we have two different technologies that both ultimately just isolate running programs into processes.
Do you know why this is?

The answer is that the fields of software engineering, computer science and system administration all are completely awful.
We do not truly *need* multiple ways to isolate programs;
the virtual machine technology exists to patch over the flaws and brokenness of legacy operating systems and programs developed by foolish people.
Such terrible software is supposedly too cool for the whole
"use the abstractions of the operating system to get access to shared resources" thing.
Instead it wants to have complete and exclusive control over everything on the system.
Almost never does it actually need that.

Unfortunately we can't really get rid of it now.
Too much terrible software has been written that assumes it's the only thing running on the machine,
and virtual machines are the easiest way to placate this terrible software.
So, given that unfortunately we need to accomodate this stupid technology,
let's consider how we might do this The Right Way.

When working at the level of processes,
there is a rich set of tools to allow users to access a machine, start some number of processes, poke at them and leave them running.
It's a common practice to give people access to a computer and allow them to start and interact with processes.
This is called a "user account" and it's about eighty million years old as a concept.
Generally for servers people get access with ``ssh`` and they use ``screen`` or ``tmux`` to manage these processes.
if we need to restrict or expand their access we can do that with normal Unix permissions,
or any of a million more fancy technologies that have grown up over the years for this express purpose.
If we need to restrict their resource usage or enforce quotas,
we again have about a million choices about how to do it, including such exciting things as ``ulimits``.

We want people to be able to start, stop, and control virtual machines;
we want to be able to restrict or expand their access;
we want to be able to restrict their resource usage and enforce quotas on them.
But woe is us, there's a lack of virtual-machine-specific features to do all these things.

Well, good!
Adding virtual-machine-specific features to our ability to control processes would be stupid!
Since virtual machines are just processes,
let's just give people user accounts, let them run virtual machines,
and profit from the eighty million years of history and technologies that this technique has on its side!

We can ``chmod 666 /dev/kvm``
and then run ``qemu`` with KVM acceleration.
To support networking, ``qemu`` will automatically use ``qemu-bridge-helper``,
a setuid binary that is configured with its own access controls in ``/etc/qemu/bridge.conf``,
which will create the necessary network devices for us to use bridged networking without root privileges.

For more advanced management and creation of VMs,
we can use the ``libvirt`` suite.
``libvirt`` comes with a daemon program which can start, stop, monitor and manage QEMU VMs.
This daemon will run just fine as an unprivileged user,
and is autostarted.
So when someone logs in to their account, they can use the ``virsh``, ``virt-install`` or other tools to create VMs easily.

If we want to limit resource usage, we can use ``ulimits`` and cgroups.
If we want to give them access to other resources such as storage, we can use normal mechanisms for giving Linux users access to storage.
The VM disk images and everything are stored (as is normal) in their home directory, and we know that any state related to this user is limited to their home directory;
a normal, useful feature of a user account.
