==========================================================================
Integrating with Club Services (Clubification)
==========================================================================

"Clubification" is "integrating with Club services".

Basic Clubification, just to get Kerberos auth:
.. code-block:: sh

    ## Install Kerberos for authentication of users
    apt-get install heimdal-clients libpam-heimdal ntp

    ## Enable SSH login with Kerberos tickets (by creating a host key)
    # get kadmin tickets (requires manually typing in /admin password)
    kinit -S kadmin/admin $CCLUB_USER/admin
    # Get a Kerberos principal for this machine (accept all defaults)
    ktutil get host/$HOSTNAME.club.cc.cmu.edu
    # actually enable Kerberos authentication
    echo 'GSSAPIAuthentication yes' >> /etc/ssh/sshd_config
    echo 'GSSAPIKeyExchange yes' >> /etc/ssh/sshd_config
    echo 'GSSAPIDelegateCredentials yes' >> /etc/ssh/ssh_config
    # restart ssh
    service ssh restart
    # if it doesn't work, compare /etc/ssh/sshd_config to a working machine

TODO also tie this into a future section talking about all the different services

- Kerberos
- AFS
- Backups
- Club packages
- Nagios
- Serial console
