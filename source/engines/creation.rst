Summoning a Daemon Engine
================================================================================

The process of creating a daemon engine is not that involved.

After installing Debian Jessie on a VM host,
it requires the following steps:

.. code-block:: sh

   # 1. Install etckeeper to track changes in /etc
   apt-get install etckeeper
   # 2. Do basic clubification to get Kerberos authentication :doc:`/lifecycle/clubification`
   # 3. Install libvirt packages
   apt-get install libvirt-bin virtinst
   # 4. Configure udev rule to grant world-access to /dev/kvm
   echo '# make kvm publicly accessible' > /etc/udev/rules.d/60-qemu-system-common.rules
   echo 'KERNEL=="kvm", GROUP="kvm", MODE="0666"' > /etc/udev/rules.d/60-qemu-system-common.rules
   # 5. Set up Hurricane Electric tunnel, see :doc:`network`
   # 5. Set up nat-bridge with system-wide libvirt, see :doc:`network`
   # 6. Set up forward-bridge, see :doc:`network`
   # 7. Configure qemu-bridge-helper to allow access to the two bridges in /etc/qemu/bridge.conf
   dpkg-statoverride --update --add root root 4755 /usr/lib/qemu/qemu-bridge-helper
   echo "allow br0" >> /etc/qemu/bridge.conf
   echo "allow virbr0" >> /etc/qemu/bridge.conf
   ln -s /usr/lib/qemu/qemu-bridge-helper /usr/bin/qemu-bridge-helper
   # 8. Insert some configuration files into /etc/skel so new user accounts are set up properly
   mkdir -p /etc/skel/.config/libvirt/qemu/networks/autostart
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
   cd /etc/skel/.config/libvirt/qemu/networks/autostart
   ln -s ../nat-bridge.xml
   ln -s ../forward-bridge.xml
