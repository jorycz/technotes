# Linux Common

## Switch to user's shell with nologin

    ### This account is currently not available
    sudo su -l www-data -s /bin/bash

## SSH and SCP for batch mode scripts

This way it ignores password prompt (it fails) and <font color="red">DANGER</font> fingerprint.

    ssh -o BatchMode=yes -o PasswordAuthentication=no -o ConnectTimeout=10 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ... <user>@<server>
    scp -q -o BatchMode=yes -o PasswordAuthentication=no -o ConnectTimeout=10 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null <src> <dst>

## SSH client config when FW dropping inactive connections

~/.ssh/config

    Host *
    ServerAliveInterval 20
    ServerAliveCountMax 3
    TCPKeepAlive no

## Locale

Generate missing locales

    locale-gen en_US.UTF-8

Alternatively a locale file can be created manually

    localedef -i en_US -f UTF-8 en_US.UTF-8

Then setup

    export LANGUAGE=en_US.UTF-8
    export LANG=en_US.UTF-8
    export LC_ALL=en_US.UTF-8
    locale-gen en_US.UTF-8
    dpkg-reconfigure locales

And / or

    echo 'LANGUAGE="en_US.UTF-8"' >> /etc/default/locale
    echo 'LC_ALL="en_US.UTF-8"' >> /etc/default/locale

## Timezone

    dpkg-reconfigure tzdata
    8. Europe
    39. Prague

## Date and Time

    date -s '2023-01-21 13:33:11'
    hwclock --systohc
    timedatectl

## Tcpdump

    tcpdump -nli eth0 -s 0 -U -w /root/tcpdump_$(date +"%d%m%Y_%H%M%S")_$(hostname).dmp host IP_ADDRESS
        ### BETTER - print ascii & data only to terminal & all to file
    tcpdump -nli eth0 -A -s 0 -U -w /root/tcpdump_$(date +"%d%m%Y_%H%M%S")_$(hostname).dmp port TCP_PORT --print

* n - don't lookup DNS names
* l - buffered output (for tail, etc ...)
* i - interface
* s 0 - store whole packets (default is 68 bytes only)
* w - store RAW packets to file instead of stdout (can be printed later using -r)
* U - do not buffer, store packets as they arrive

For Ethereal/Wireshark in case of SSL (not PFS) in Settings > SSL protocol set in RSA key list `server IP`, `port`, `ssl key` and protocol `http`. Then use `Follow TCP stream`.

## Start something after boot - Legacy examples

/etc/rc.local

    /root/startOnBoot.sh &> /tmp/startOnBoot.log
    exit 0   ### end of rc.local

Or run script with environment setup

    nohup su root --shell /bin/bash --command "/root/vncserver.sh start > /tmp/vncserver.sh.log" &
    exit 0   ### end of rc.local

/root/startOnBoot.sh

    #!/bin/bash

    ### Run under user
    /sbin/runuser -l jr -c 'vncserver -localhost no' &> /home/${USER}/vncserver.start.from.root
    ### Run under root with security limits.conf applied (probably bug)
    su root --shell /bin/bash --command "/bin/stunnel /etc/stunnel/stunnel.conf"

Beware of `-localhost no` which setup vnc to listen on ALL interfaces.

## Set HTTP proxy (environment, curl, wget)

/etc/environment

    export http_proxy="http://${HOST}:${PORT}/"
    export https_proxy="http://${HOST}:${PORT}"
    export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"
    export HTTP_PROXY="http://${HOST}:${PORT}/"
    export HTTPS_PROXY="http://${HOST}:${PORT}/"
    export NO_PROXY="localhost,127.0.0.1,localaddress,.localdomain.com"

~/.curlrc

    proxy=${HOST}:${PORT}

* `--noproxy '*'`
* `--proxy ${HTTP_PROXY}`

~/.wgetrc

    use_proxy = on
    https_proxy = http://${HOST}:${PORT}/
    http_proxy = http://${HOST}:${PORT}/
    ftp_proxy = http://${HOST}:${PORT}/

Java

    export JAVA_OPTS="-Dhttps.proxyHost=${HOST} -Dhttps.proxyPort=${PORT}"
    export JAVA_FLAGS="-Dhttps.proxyHost=${HOST} -Dhttps.proxyPort=${PORT}"

## curl - post binary to WEB form

    curl -v -F uploadBtn=Upload -F uploadedFile=@IMAGE_098.jpg https://some.domain.cz/upload.php

## curl - set timeouts

    CURL="/usr/bin/curl --connect-timeout 5 --max-time 10"
    ### Then use like
    ${CURL} -s -H "Content-type: application/json; charset=UTF-8" ...

## Disable auto-lock, screen autolock, screensaver

    apt purge light-locker

## Enable auto-login

/etc/lightdm/lightdm.conf

    [Seat:*]
    #type=local
    ...
    autologin-user=doma
    autologin-user-timeout=5

/etc/gdm3/custom.conf

    [daemon]
    AutomaticLoginEnable=True
    AutomaticLogin=username

## Show information about HW, Hardware model etc

    dmidecode | grep -A3 '^System Information'

## Find Network Ethernet PCI ID name

    grep PCI_SLOT_NAME /sys/class/net/*/device/uevent

## Backup / Restore package versions

Backup current version of all installed packages

    sudo apt install dpkg-dev
    touch ~/installed_packages.txt
    dpkg-query -l > ~/installed_packages.txt

Reinstall versions later

    sudo dpkg --clear-selections
    sudo dpkg --set-selections < ~/installed_packages.txt
    sudo apt-get dselect-upgrade

## Check 3D-Acceleration is working (HW/VM)

    apt install mesa-utils nux-tools
    /usr/lib/nux/unity_support_test -p   ### ALL must be yes
    glxinfo -B   ### must be Device: virgl (Apple ... neco ...)

## Turn OFF resising of inodes on FS

    tune2fs -O ^resize_inode /dev/md0

## Youtube Downloader

Download best video and audio quality  
Install using pip `pip3 install --upgrade youtube-dl`  
Or install manually [youtube-dl](https://github.com/ytdl-org/youtube-dl). For merge `ffmpeg` or `avconv` are needed. Then use it like:

    ./youtube-dl -f bestvideo+bestaudio "URL"

