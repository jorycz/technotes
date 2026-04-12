# Raspberry Pi - Common

## Installation

[Raspberry Pi Imager](https://www.raspberrypi.org/software/) with `No Desktop Environment` or manually.

### Manual installation

Mount FAT part of the SD card (**/boot**) and create:

empty ssh file to enable SSH

    touch ssh

userconf to create user with password (111111) during installation with content appuser:<hashed password>

    appuser:$6$PN6BjrMZoDZNba3C$U7dt6QMKaMD/WnTgM5OyLGgYZo7KMAN0aAphXdq9NOFSS1yi4MDYLbdlil7EMqQleLCTGOJXBldBSk8phoBxr.

wpa_supplicant.conf

    country=CZ
    update_config=1
    ctrl_interface=/var/run/wpa_supplicant
    network={
     scan_ssid=1
     ssid="WIFI_SSID"
     psk="WIFI_PASSWORD"
    }

* If the password contains characters other than ASCII, it must be written in HEX format `echo -n "PASSWORD" | od -t x1 -A n -w100000 | tr -d ' '`

Static IP address can also be set in `/etc/dhcpcd.conf`, but rootfs (ext4) must be mounted.

    interface eth0
    static ip_address=10.x.x.x/24
    static routers=10.x.x.x
    static domain_name_servers=10.x.x.x 185.x.x.x

## Basic packages

    apt -y dist-upgrade
    apt install -y software-properties-common vim git

## Basic configuration

Time synchronization shoudl be active

    timedatectl status
    ### timedatectl set-time '2022-12-14 13:14:15'
    timedatectl list-timezones |  egrep  -o "Europe/P.*"
    ### timedatectl set-timezone Europe/Prague
    ### OR raspi-config > Localisation Options > Timezone

Check and setup time synchronization

    timedatectl show-timesync
    timedatectl timesync-status
    timedatectl ntp-servers ... ... ...

or set NTP in /etc/systemd/timesyncd.conf

    [Time]
    ...
    #FallbackNTP=0.debian.pool.ntp.org 1.debian.pool.ntp.org 2.debian.pool.ntp.org 3.debian.pool.ntp.org
    ...

