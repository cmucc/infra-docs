IPv6
================================================================================
Currently we get IPv6 from Hurricane Electric tunnels.

There is a tunnel configured on each daemon engine.
It would probably be better to instead have a single tunnel on some router,
which can run IPv6 stateless autoconfig to give IPv6 address to everything in the room.
But that's slightly more annoying to set up.
Also, the tunnels have a bandwidth limit, so multiple tunnels mitigates that.

How to set up tunnel:

1. Allocate tunnel on https://www.tunnelbroker.net/
2. Copy and paste their Debian/Ubuntu configuration into ``/etc/network/interfaces``
3. For support for handing out these IPv6 addresses on a libvirt virtual network,
   add the following snippet of network XML to the network.

.. code-block:: xml

   <ip family='ipv6' address='2001:dead:beef:a::1' prefix='64'/>

