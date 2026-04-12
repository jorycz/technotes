# Raspberry Pi - Ramdisk

## /etc/fstab

    tmpfs /tmp tmpfs defaults,noatime,nosuid,mode=1777,size=48m 0 0
    tmpfs /var/tmp tmpfs defaults,noatime,nosuid,mode=1777,size=8m 0 0
    tmpfs /var/mail tmpfs defaults,noatime,nosuid,mode=2775,size=4m 0 0
    tmpfs /var/log tmpfs defaults,noatime,nosuid,mode=0755,size=96m 0 0
    # tmpfs /var/spool tmpfs defaults,noatime,nosuid,mode=0755,size=4m 0 0   # - user crontabs ...

Check what folders already exists and create them when mounting using `/etc/tmpfiles.d/beNiceToYourSDCard.conf` otherwise non-default services wont start (like apache2 or powerdns ...)

## /etc/tmpfiles.d/beNiceToYourSDCard.conf

    #  This file is part of systemd.
    #
    #  systemd is free software; you can redistribute it and/or modify it
    #  under the terms of the GNU Lesser General Public License as published by
    #  the Free Software Foundation; either version 2.1 of the License, or
    #  (at your option) any later version.

    # See tmpfiles.d(5) for details

    ### /tmp ###
    d /tmp 1777 - - -

    ### /var/tmp ###
    d /var/tmp 1777 - - -

    ### /var/mail ###
    d /var/mail 2775 root mail -

    # q /var 0755 - - -
    # L /var/run - - - - ../run
    ### /var/log ###
    d /var/log 0755 - - -
    f /var/log/wtmp 0664 root utmp -
    f /var/log/btmp 0600 root utmp -
    ### Below dirs need to be created in /var/log(tmpfs)
    d /var/log/apache2 750 root adm -
    # d /var/log/mysql 2750 mysql adm -
    # d /var/log/redis 750 redis redis -
    d /var/log/apt 755 root root -
    # d /var/log/ntpstats 755 ntp ntp -
    # d /var/log/samba 750 root adm -
    # d /var/log/systat 755 root root -
    d /var/log/journal 2755 root systemd-journal -
    d /var/log/openvpn 0755 - - -
    d /var/log/private 0700 - - -
    d /var/log/runit 0755 - - -
    ### Below files need to be created in /var/log(tmpfs)
    f /var/log/lastlog 0664 root utmp -
    f /var/log/faillog 0644 root utmp -

    ### /var/spool ###
    # d /var/spool 0755 - - -
    # d /var/spool/rsyslog 0700 - - -
    # d /var/spool/powerdns 0755 - - -
    # d /var/spool/postfix 0755 - - -

    ### /var/cache ###
    # d /var/cache 0755 - - -

    ### /var/lib ###
    # d /var/lib 0755 - - -

## Check failed units after reboot if any

    systemctl list-units --failed
    systemctl list-units --state failed


