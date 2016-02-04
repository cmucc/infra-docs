==========================================================================
Networking Club Servers
==========================================================================

TODO:
IPv6 on daemon engines,
Internal networking

Generally, to get internet access and an IP address for a machine,
you need to go through NetReg_.
NetReg (short for Network Registration) is a website operated by Computing Services.
Since Computing Services operates our DHCP server,
we need to go through NetReg if we want to use DHCP;
and even if we don't want to use DHCP, we use NetReg to avoid IP address collision.

Here is the url for NetReg_.
Log in as cl0x, if you can, or your own Andrew user, if it's been granted access to register in CClub space.
If you register as your own user,
then when you graduate you'll have to migrate all of those registrations to cl0x.

.. _NetReg: https://wiki.club.cc.cmu.edu/org-auth/ccwiki/NetReg

Registering a machine on NetReg
================================
1. Click Register New Machine.
2. Select "Subnet: Computer Club" and click Continue.
3. Fill in the hostname of the machine, and choose "CLUB.CC.CMU.EDU" as the suffix.
4. Fill in the MAC address of the physical machine;
   use the one corresponding to the first numbered ethernet port on the back of the machine,
   and make sure to plug the ethernet cable into that port as well.
   If you're registering a VM, don't fill in the MAC address right now.
5. Click Continue at the bottom of the page.
6. Reopen the page for that record to see what IP address you were assigned,
   and verify that you filled in the things correctly.
7. If you're registering a VM, you now need to fill in the MAC address.
   Run this to generate the MAC address from the IP address, fill it in, and hit "Update".

   .. code-block:: sh
   
       printf "0000%02x%02x%02x%02x\n" 128 237 157 XXX
       # This MAC address corresponds to Cray ethernet hardware,
       # which will never be in B6 anyway
   
8. Wait for DHCP to propagate.

   If you click "Signout",
   you can see when the next DHCP server update happens,
   so you don't have to just wait for an indeterminate amount of time.

Set PXE boot parameters
=======================
This is now unnecessary,
because we told Computing Services to change the defaults for all machines to match this,
so we no longer have to specifically set it.

1. Open the page for the appropriate NetReg record.
2. Click the "View Advanced Options" link.
3. Scroll down to the "DHCP Options" section and click the "Add DHCP Option" link.
4. Use this to add two options:

   .. code-block:: yaml
   
      filename: "/netinstall-wheezy/debian-installer/amd64/pxelinux.0"
      next-server: storage-2.club.cc.cmu.edu

5. Wait for DHCP to propagate.

   If you click "Signout",
   you can see when the next DHCP server update happens,
   so you don't have to just wait for an indeterminate amount of time.
