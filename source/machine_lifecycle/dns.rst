==========================================================================
Working with DNS 
==========================================================================

So you may have an IP address for your new machine,
but you want a nice domain name too,
so you can ``ssh awesome.club.cc.cmu.edu`` instead of ``ssh 128.237.157.1``.
Well, then you need to tweak our DNS record data!

Computer Club DNS uses the djbdns DNS server.
The record data for this program is stored in a format which is documented elsewhere_.
Our DNS setup is detailed at
https://wiki.club.cc.cmu.edu/org-auth/ccwiki/Services/Club%20DNS

.. _elsewhere: http://cr.yp.to/djbdns/tinydns-data.html

The Computer Club DNS record data lives in ``/afs/club.cc.cmu.edu/service/dns``.
This is a clone of a git repository which lives in ``/afs/club.cc.cmu.edu/git/service/dns``.
Both of these directories require an administrator AFS token for write access.

There is a helpful tool ``tinydns-edit`` in ``/afs/club.cc.cmu.edu/service/dns``,
which does some validation before adding records.
You might consider using it instead of hand-editing.

Adding a new machine
====================

You should add a record with a "=" in front.
That will add both an A (forward) record and PTR (reverse) record.

Run the following commands, setting the appropriate variables.

   .. code-block:: sh

      cd /afs/club.cc.cmu.edu/service/dns
      kinit $USER/admin@CLUB.CC.CMU.EDU
      aklog CLUB.CC.CMU.EDU

      # NAME = the name of the new machine
      # IP = the IP of the new machine

      # this will remove the old host record; there must only be one
      sed -i "/^=.*:$IP:.*/d" DB.club.cc.cmu.edu

      ./tinydns-edit DB.club.cc.cmu.edu DB.club.cc.cmu.edu~ add host $NAME.club.cc.cmu.edu $IP

      # review the changes for sanity
      git diff
      git add DB.club.cc.cmu.edu
      git commit -m "I did something"

Adding an additional A record for existing machine
==================================================

You should add a record with a "+" in front.
That will add just an A (forward) record.

Run the following commands, setting the appropriate variables.

   .. code-block:: sh

      cd /afs/club.cc.cmu.edu/service/dns
      kinit $USER/admin@CLUB.CC.CMU.EDU
      aklog CLUB.CC.CMU.EDU

      # NAME = the name of the new machine
      # IP = the IP of the new machine

      ./tinydns-edit DB.club.cc.cmu.edu DB.club.cc.cmu.edu~ add alias $NAME.club.cc.cmu.edu $IP

      # review the changes for sanity
      git diff
      git add DB.club.cc.cmu.edu
      git commit -m "I did something"
