================================================================================
Networking
================================================================================

Currently, daemon hosts have two networks usable by unprivileged virtual machines.
You can see these with ``virsh net-list`` on a properly configured user account.

forward-bridge
=================

This is just a Linux bridge (generally ``br0``) connected to our physical network,
set up with ``/etc/network/interfaces``.
It means your VM is exactly as functional as a physical machine when it comes to networking.

To actually use this, you'll need to make a NetReg entry for a VM; see :doc:`/lifecycle/network`.

After you have a NetReg entry and a corresponding MAC address,
you can use this network by adding this argument to ``virt-install``:
``--network network=forward-bridge,mac=yo:ug:ot:fr:om:nr``

nat-bridge
=================

This is also just a Linux bridge (generally ``virbr0``),
but it's maintained by the system-wide libvirt daemon,
which runs ``dnsmasq`` on the bridge to provide NAT.
This is mostly just the deafult libvirt system-wide network.

We also configure it to provide IPv6 connectivity and public IPv6 addresses.
by adding the following snippet of network XML to the network.

.. code-block:: xml

   <ip family='ipv6' address='2001:dead:beef:a::1' prefix='64'/>

The address there should be the prefix that we receive from Hurrican Electric.

Since this network uses NAT for IPv4 connectivity, it's not publicly accessible over IPv4.
However, since it's still accessible over IPv6,
we can use some tricks to make it useful.
Notably, we can use Cloudflare to proxy HTTP/HTTPS connections and direct them to machines on this network.

You can use this network by adding this argument to ``virt-install``:
``--network network=nat-bridge``

IPv6 connectivity
=================

Currently we get IPv6 connectivity, and a delegation of IPv6 addresses,
from Hurricane Electric tunnels.
There is a tunnel configured on each daemon engine.

It would probably be better to instead have a single tunnel on some router,
which can run IPv6 stateless autoconfig to give IPv6 address to everything in the room.
But that's slightly more annoying to set up.
Also, the tunnels have a bandwidth limit, and multiple tunnels mitigates that.

How to set up tunnel:

1. Allocate tunnel on https://www.tunnelbroker.net/
2. Copy and paste their Debian/Ubuntu configuration into ``/etc/network/interfaces``

Cloudflare
==========

The Cloudflare login information is in ``/afs/club/admin/passwords/``;
you can use that and make a DNS AAAA record on Cloudflare to use IPv4-to-IPv6 proxying,
for use with nat-bridge.

Finding out a VM's IP address
==============================

After installing a VM, you won't necessarily know the IP address of that VM;
there's nothing that will tell you.
But you can do this:

.. code-block:: sh
   ip neighbour | grep -Ff <(virsh dumpxml $MYVMNAME | xpath -q -e '//mac/@address' | cut -d '"' -f 2) | cut -d ' ' -f 1

The ``<()`` thing is called "process substitution",
if you're trying to understand what this command does.

We're just grepping in the neighbour tables of the host for the VM's MAC address,
and dumping the resulting IPs.
