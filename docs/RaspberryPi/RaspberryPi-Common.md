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
    apt install -y software-properties-common vim git jq

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

## Network configuration after boot

Find and remove wpa_supplicant.conf

    sudo find /etc -name wpa_supplicant.conf -delete
    sudo find /boot -name wpa_supplicant.conf -delete

Create hashed password

    wpa_passphrase "<WIFI_SSID>"
    ### optionally (put space in the front of command):  wpa_passphrase "<WIFI_SSID>" "<WIFI_PASSWORD>"

Create Network Manager config **/etc/NetworkManager/system-connections/my.nmconnection**

    [connection]
    id=my
    type=wifi
    interface-name=wlan0
    autoconnect=true

    [wifi]
    mode=infrastructure
    ssid=<WIFI_SSID>

    [wifi-security]
    key-mgmt=wpa-psk
    psk=<WIFI_PASSWORD>

    [ipv4]
    method=auto

    [ipv6]
    method=auto

Setup rights and enable configuration

    sudo chmod 600 /etc/NetworkManager/system-connections/my.nmconnection
    sudo nmcli connection reload
    sudo nmcli connection up my

Verify

    nmcli dev                ### nmcli device status
    nmcli connection show
    nmcli connection show my
    iw dev wlan0 link
    iw reg get               ### info about frequency bands in current country
    iw dev wlan0 station dump
    sudo iw dev wlan0 scan | grep SSID

In case of need to restart everything

    sudo nmcli radio wifi off && sleep 2 && sudo nmcli radio wifi on
    sudo systemctl restart systemd-networkd
    sudo systemctl restart NetworkManager

In case of need to debug of wpa_supplicant

    sudo dmesg -wT
    sudo systemctl stop wpa_supplicant
    sudo wpa_supplicant -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf -dd

--
