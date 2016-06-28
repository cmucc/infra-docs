Summoning a Daemon Engine
========================================

*Note: It would be nice if this was more automated.*

Hardware concerns
-----------------

Daemon engines should be among the most powerful systems we have.

For Dell 2950s in particular, you should select systems with two quad-core
processors and 32 GB of RAM.

They should also have two mirrored 1 TB hard drives.

Disk partitioning
..................

Generally, you will want three partitions:

#. A small (10 GB) partition for the system.
#. A small (4 GB) swap partition.
#. A partition for ZFS.  A ZFS filesystem will eventually be mounted on
   ``/home``.

There should be two identical disks in each server partitioned the same way.
The system and swap partitions should be mirrored (RAID 1) with mdraid.

The ZFS partition should be left unallocated.

Note: you should also ``grub-install`` to both disks.

Install etckeeper
-----------------

See the clubification page for justification.

.. code-block:: sh

   apt-get install etckeeper

Clubification
-----------------

Do it.  **TODO: Add link**

ZFS Setup
-----------------

Install ZFS packages
......................

**Note:** This process is subject to change as ZFS on Linux gains increasing
adoption.  In particular, there are vague plans to get ZFS on Linux source
packages into Debian (but not binaries, due to licensing conflicts).

Follow the instructions on http://zfsonlinux.org/debian.html.
If they say anything about the Debian project having added source packages,
update these instructions or get someone else to do so.

Note: there are now packages in Debian unstable for ZFS.  Revisit this after
the release of Debian 9.

Setup zpool
..................

Create a mirrored pool out of the ZFS partitions,
and enable lz4 compression by default on the zpool's root filesystem.

(Replace ``/dev/sda6`` and ``/dev/sdb6`` with the ZFS partitions)

.. code-block:: sh

   zpool create tank mirror sda6 sdb6
   zfs set compression=lz4 tank

Setup filesystems
..................

Create a filesystem for ``/home``.

.. code-block:: sh

   zfs create tank/home -o mountpoint=/home

Create a filesystem for the OpenAFS cache, with a refreservation equal to
the cache size to ensure that the cache cannot run out of space.
Replace "100M" if the value set differs.

.. code-block:: sh

   zfs create tank/afs_cache -o mountpoint=/var/cache/openafs -o refreservation=100M

Local home directories
------------------------

.. code-block:: apache

   echo 'map passwd homeDirectory "/home/$uid"' >> /etc/nslcd.conf

   echo "session required pam_mkhomedir.so" >> /etc/pam.d/common-session

   service nscd restart
   service nslcd restart


KVM
-----------------

Configure apt-pinning
......................

There is unfortunately a very nasty bug relating to how ``virt-resize`` handles
extended partitions that is only fixed in Debian testing. [#]_ [#]_ [#]_

.. [#] Bug report: https://bugzilla.redhat.com/show_bug.cgi?id=1169015
.. [#] Fixed in https://github.com/libguestfs/libguestfs/commit/9d6f0b6a86d68438b27a3d783677c63f39ec6627
.. [#] That corresponds to tag 1.29.14, while Debian Jessie has 1.28.1. See https://packages.debian.org/jessie/libguestfs-tools

.. code-block:: sh

   apt-get install libguestfs-tools

Check whether or not jessie-updates has something newer than 1.28.1; poke ``hillu`` on Freenode at a respectful frequency if it does not.  (Ask ``grantwu`` if not sure).

Install virtualization packages
...............................

Install libvirt packages and
allow unprivileged access to ``/dev/kvm`` using udev.

.. code-block:: sh

   apt-get install libvirt-bin virtinst
   echo '# make kvm publicly accessible' > /etc/udev/rules.d/60-qemu-system-common.rules
   echo 'KERNEL=="kvm", GROUP="kvm", MODE="0666"' >> /etc/udev/rules.d/60-qemu-system-common.rules

Networking
-----------------

nat-bridge setup
..................
You will need to get a Hurricane Electric tunnel.
Allocate a tunnel on https://www.tunnelbroker.net/.
We currently don't have a systematic way for figuring out which Tunnelbroker account is responsible for the tunnel on any particular VM, but we should.

Copy and paste the Debian/Ubuntu configuration they provide into
``/etc/network/interfaces``.

Then add the following snippet of XML to
``/etc/libvirt/qemu/networks/default.xml``,
**but substituting in the prefix received from Hurricane Electric**.

.. code-block:: xml

   <ip family='ipv6' address='2001:dead:beef:a::1' prefix='64'/>

forward-bridge setup
.....................

Add this to ``/etc/network/interfaces``:

.. We need a better code-block language here
.. code-block:: apache

   iface eth0 inet manual

   auto br0
   iface br0 inet dhcp
	 bridge_ports eth0
	 bridge_stp off

Then run

.. code-block:: sh

  systemctl restart networking
  virsh net-start default
  virsh net-autostart default

qemu-bridge-helper setup
.........................

Configure qemu-bridge-helper to allow access to the two bridges in
``/etc/qemu/bridge.conf``:

.. code-block:: sh

   dpkg-statoverride --update --add root root 4755 /usr/lib/qemu/qemu-bridge-helper
   echo "allow br0" >> /etc/qemu/bridge.conf
   echo "allow virbr0" >> /etc/qemu/bridge.conf
   ln -s /usr/lib/qemu/qemu-bridge-helper /usr/bin/qemu-bridge-helper

/etc/skel configuration
........................

.. code-block:: sh

   # Create the necessary directories
   mkdir -p /etc/skel/.config/libvirt/qemu/networks/autostart
   # Add the definitions of the nat-bridge and the forward-bridge
   cat >/etc/skel/.config/libvirt/qemu/networks/nat-bridge.xml <<XML_EOF
   <network>
     <!--
     uuid has to be in here or some versions of libvirt go crazy; it
     loads this network definition in, defines a uuid, puts it in
     its runtime cache, but it doesn't actually persist the uuid
     back. check if this bug has been fixed, future generations!
     -->
     <uuid>a6720aef-dbcf-4900-88e8-beea8a5fa8ed</uuid>
     <name>nat-bridge</name>
     <forward mode='bridge'/>
     <bridge name='virbr0'/>
   </network>
   XML_EOF
   cat >/etc/skel/.config/libvirt/qemu/networks/forward-bridge.xml <<XML_EOF
   <network>
     <!--
     uuid has to be in here or some versions of libvirt go crazy; it
     loads this network definition in, defines a uuid, puts it in
     its runtime cache, but it doesn't actually persist the uuid
     back. check if this bug has been fixed, future generations!
     -->
     <uuid>df6bea2c-12e2-44ed-921f-0e0156a34b28</uuid>
     <name>forward-bridge</name>
     <forward mode='bridge'/>
     <bridge name='br0'/>
   </network>
   XML_EOF
   # Set these networks to be autostarted
   cd /etc/skel/.config/libvirt/qemu/networks/autostart
   ln -s ../nat-bridge.xml
   ln -s ../forward-bridge.xml
