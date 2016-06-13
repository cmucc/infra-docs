Clubification
==========================================================================

"Clubification" encompasses:

* Installing various packages useful for system administration
* Setting up :doc:`/accounts/ldap`.  LDAP is a user lookup system for multiple accounts.
* Setting up :doc:`/core_systems/kerberos/index`.  Kerberos is a user authentication protocol.
* Setting up :doc:`/core_systems/afs/index`.  AFS is a convenient distributed filesystem
  with Kerberos integration.

Clubification packages
---------------------------

.. code-block:: sh

   ## Enable CClub packages
   echo "deb http://debian.club.cc.cmu.edu/debian/ jessie-cclub contrib" >> /etc/apt/sources.list
   # Trust the CClub signing key for packages (as well as the CClub CA)
   # Note that you will get a warning from apt that "The following signatures couldn't be verified because the public key is not available"
   # This is mildly insecure, but this package is from the local network, and we trust the local network
   apt-get update && apt-get install cclub-keyring && apt-get update

   # TODO: Figure out a better way to version control and present this
   apt-get install aptitude apt-show-versions atool build-essential curl emacs-nox finger git htop lftp moreutils mosh mtr-tiny ncdu ncftp netcat ntp python3 ranger rsync screen smartmontools strace sudo tree tcsh tmux unzip vim zip zsh

LDAP and Kerberos
---------------------------

.. code-block:: sh

   ## Install LDAP so the machine knows about club users, and Kerberos for authentication of those users
   # Note that you should select the "passwd" and "group" for name services for LDAP to provide
   apt-get install libnss-ldapd heimdal-clients libpam-heimdal

   ## Enable SSH login with Kerberos tickets (by creating a host key)
   # Get Kerberos admin tickets (requires manually typing in /admin password)
   kinit -S kadmin/admin <username>/admin
   # Create a Kerberos principal for this machine (accept all defaults)
   ktutil get host/$HOSTNAME.club.cc.cmu.edu

   ## Actually enable Kerberos authentication
   # If the machine's time is off then ticket authentication will mysteriously fail
   # ntp should be installed already as a clubification package, but if it's not:
   # apt-get install ntp
   # modify ssh configuration to accept Kerberos tickets
   echo 'GSSAPIAuthentication yes' >> /etc/ssh/sshd_config
   echo 'GSSAPIKeyExchange yes' >> /etc/ssh/sshd_config
   echo 'GSSAPIDelegateCredentials yes' >> /etc/ssh/ssh_config
   service ssh restart

   ## Setup ksu
   # TODO Make this into a Debian package
   # Autopopulate .k5login based on membership of wheel group
   # This allows people to log in as root via ssh or ksu using their $USER/root principals
   # Note the quotes
   echo -e '#!/bin/bash\n'"getent group wheel | cut -d : -f 4 | tr , '\\\\n' | sed 's/$/\/root@CLUB.CC.CMU.EDU/' > /root/.k5login" > /etc/cron.hourly/update-root-k5login
   chmod 755 /etc/cron.hourly/update-root-k5login

   # Make ksu setuid root, so it can be used like sudo -i to get root privileges
   dpkg-statoverride --update --add root root 4755 /usr/bin/ksu

AFS
---------------------------

Note that AFS should only be installed on appropriate machines.

.. code-block:: sh

   apt-get install openafs-client libpam-afs-session openafs-krb5

Note that you should set the AFS cell to ``club.cc.cmu.edu`` and the cache size
to a reasonable value like 100000 kilobytes (100 megabytes).

Miscellaneous
---------------------------

Configure root mail to alias to gripe@club.cc.cmu.edu by editing ``/etc/aliases``.  You will need to ``dpkg-reconfigure exim4-config`` to get Maildir format.



TODO: Figure out Nagios?  Serial console?
