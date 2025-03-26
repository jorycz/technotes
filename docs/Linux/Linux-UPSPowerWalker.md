# Linux UPS Power Walker

## Model PowerWalker VI 650 IEC

* USB version driver [balzer_usb](https://networkupstools.org/docs/man/blazer_usb.html)
* Driver find on [networkupstools.org](https://networkupstools.org/stable-hcl.html)

## Installation and Setup

    apt-get install nut

## /etc/nut/nut.conf

    MODE=standalone

## /etc/nut/ups.conf

    [powerwalker]
        driver = blazer_usb
        port = auto
        desc = "PowerWalker UPS"

## Restart server

    systemctl restart nut-server   ### OR upsdrvctl start

## /etc/nut/upsd.users

    [local]
        password = PASSWORD
        upsmon master
        actions = SET
        instcmds = ALL

## /etc/nut/upsmon.conf

    ...
    NOTIFYFLAG ONLINE       SYSLOG+WALL+EXEC
    NOTIFYFLAG ONBATT       SYSLOG+WALL+EXEC
    NOTIFYFLAG LOWBATT      SYSLOG+WALL+EXEC
    NOTIFYFLAG FSD          SYSLOG+WALL+EXEC
    NOTIFYFLAG COMMOK       SYSLOG+WALL+EXEC
    NOTIFYFLAG COMMBAD      SYSLOG+WALL+EXEC
    NOTIFYFLAG SHUTDOWN     SYSLOG+WALL+EXEC
    NOTIFYFLAG REPLBATT     SYSLOG+WALL+EXEC
    NOTIFYFLAG NOCOMM       SYSLOG+WALL+EXEC
    NOTIFYFLAG NOPARENT     SYSLOG+WALL+EXEC
    ...
    MONITOR powerwalker@localhost 1 local PASSWORD master
    SHUTDOWNCMD "sleep 5 ; /sbin/shutdown -h now"
    NOTIFYCMD "/usr/local/bin/upsNotify.sh"
    # POWERDOWNFLAG /etc/killpower

## /usr/local/bin/upsNotify.sh

    #!/bin/bash

    EMAIL="someone@somewhere.cz"
    LOG="/tmp/upsMessages.log"
    U=$(whoami)
    MESSAGE="${U} @ $NOTIFYTYPE / ${1}."
    M_BATT="NAS will shutdown in 1 minute and cmd in 2 minutes."
    M_WALL="Wall power restored."

    echo "--------- $(date) ---------" >> ${LOG}
    echo "${U} @ $NOTIFYTYPE / $1" >> ${LOG}

    if [[ "$NOTIFYTYPE" =~ (ONBATT|LOWBATT|FSD|SHUTDOWN) ]]
    then
        ### Shutdown NAS in 1 minute
        MESSAGE="${MESSAGE} ${M_BATT}"
        echo 'ssh -i /usr/local/bin/upsNotify.sh.id_rsa -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null USER@HOSTNAME_NAS "sudo poweroff"' | at now + "1 minutes"
        echo 'sudo /sbin/poweroff' | at now + "2 minutes"
        echo "${MESSAGE}" >> ${LOG}
        atq >> ${LOG}
    fi

    if [[ "$NOTIFYTYPE" =~ (ONLINE) ]]
    then
        ### Cancel all scheduled poweroff jobs if any
        MESSAGE="${MESSAGE} ${M_WALL}"
        echo "Trying to cancel shutdowns ..." >> ${LOG}
        atq >> ${LOG}
        for i in `atq | awk '{print $1}'` ; do atrm $i ; done
    fi

    echo "${MESSAGE}" | mail -s "UPS on cmd ($NOTIFYTYPE)" ${EMAIL}

    echo "-----------------------------------------" >> ${LOG}

## /etc/rc.local - disable beeper

    upscmd -u local -p PASSWORD powerwal beeper.toggle &

## Start NUT and check /var/log/daemon.log

    /etc/init.d/nut-server start  ### OR reboot

## Runtime settings

Command `upsc powerwalker`

    Init SSL without certificate database
    battery.charge: 92
    battery.voltage: 12.80
    battery.voltage.high: 13.00
    battery.voltage.low: 10.40
    battery.voltage.nominal: 12.0
    device.type: ups
    driver.name: blazer_usb
    driver.parameter.pollinterval: 2
    driver.parameter.port: auto
    driver.version: 2.7.2
    driver.version.internal: 0.11
    input.current.nominal: 2.0
    input.frequency: 50.3
    input.frequency.nominal: 50
    input.voltage: 228.4
    input.voltage.fault: 228.4
    input.voltage.nominal: 230
    output.voltage: 228.4
    ups.beeper.status: disabled
    ups.delay.shutdown: 30
    ups.delay.start: 180
    ups.load: 6
    ups.productid: 5161
    ups.status: OL
    ups.temperature: 25.0
    ups.type: offline / line interactive
    ups.vendorid: 0665

Command `upscmd -l powerwalker`

    Instant commands supported on UPS [powerwal]:
    beeper.toggle - Toggle the UPS beeper
    load.off - Turn off the load immediately
    load.on - Turn on the load immediately
    shutdown.return - Turn off the load and return when power is back
    shutdown.stayoff - Turn off the load and remain off
    shutdown.stop - Stop a shutdown in progress
    test.battery.start - Start a battery test
    test.battery.start.deep - Start a deep battery test
    test.battery.start.quick - Start a quick battery test
    test.battery.stop - Stop the battery test

Disable beeper adhoc by `upscmd -u local -p PASSWORD powerwalker beeper.toggle`
