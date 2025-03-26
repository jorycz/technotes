# Linux Systemd

## Basics

    sudo systemctl daemon-reload
    sudo systemctl enable some.service
    sudo systemctl start some.service
    sudo systemctl is-active some.service
    sudo systemctl restart some.service
    sudo systemctl stop some.service
    sudo systemctl cat some.service
    sudo systemctl disable some.service

## Remove service

Stop and Disable service.

    rm /etc/systemd/system/SERVICE_NAME
    rm /usr/lib/systemd/system/SERVICE_NAME
    systemctl daemon-reload
    systemctl reset-failed

## journalctl - show log of service

**TAIL LOG**

    journalctl -f -u SERVICE_NAME.service

Show all log messages

    journalctl -u SERVICE_NAME.service

Just since boot

    journalctl -b -u SERVICE_NAME.service

**cat** like style

    journalctl -b --no-pager -u SERVICE_NAME.service

Show log from the END

    journalctl -b -e -u SERVICE_NAME.service

cat RAW log (same way as process)

    journalctl -f -b -u SERVICE_NAME.service -o cat

## journald maintenance

Maintenance of `/var/log/journal/4c74c042939f4af8971cc0decb93d3d8/...` for example.

    journalctl --disk-usage
    journalctl --vacuum-size=500M

Set desired values in `/etc/systemd/journald.conf`

    SystemMaxUse=1024M
    MaxRetentionSec=1month

And restart daemon `systemctl restart systemd-journald.service`. More info on [SO](https://unix.stackexchange.com/questions/130786/can-i-remove-files-in-var-log-journal-and-var-cache-abrt-di-usr)

## Edit existing unit

    systemctl edit --full systemd-networkd-wait-online.service

Add **TimeoutStartSec=5** in `[Service]` section, if Ubuntu start taking forever ...

## BASH script to unit - AltServer example

altserver.service

    [Unit]
    Description=Alt Server
    Wants=network-online.target
    After=network-online.target

    [Service]
    User=root
    Group=root
    Type=simple
    TimeoutStartSec=30
    Restart=on-failure
    RestartSec=30s
    WorkingDirectory=/root
    ExecStart=/root/runAltLinux.sh
    SyslogIdentifier=AltServer

    [Install]
    WantedBy=multi-user.target

runAltLinux.sh

    #!/bin/bash

    while true
    do
        RUN_DATE=$(date +"%Y%m%d_%H%M%S")
        RUN_SESSION="alt_${RUN_DATE}"
        tmux new-session -d -s "${RUN_SESSION}"
        tmux send-keys -t "${RUN_SESSION}:0" "while true ; do python3 main.py ; sleep 30 ; echo ; echo ; echo ; done" Enter
        sleep infinity
        sleep 2147483647
    done

