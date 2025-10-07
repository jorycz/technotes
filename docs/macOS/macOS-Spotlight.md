# macOS Spotlight

## DB Reset

Run as owner for network homes, otherwise run as root.

    mdutil   ### To show options ...

Show Status and Workers

    mdutil -asv
    ps -ceo command | grep -c mdworker

Global - Disable and Reset

    sudo mdutil -a -i off
    sudo mdutil -aE
    sudo rm -rf /System/Volumes/Data/.Spotlight-V100/

User - Delete Spotlight now

    rm -rf Library/Metadata/CoreSpotlight/
    sudo pkill mds
    sudo pkill mds_stores
    sudo pkill mdbulkimport

* You can try `mdutil -X` also.

Enable without Reboot

    sudo mdutil -a -i on
    sudo killall Spotlight
  
Enable with Reboot

    ### ... REBOOT ...
    sudo mdutil -a -i on

## If icon is in wrong state, restart UI Server

    killall SystemUIServer

## Do NOT index HDD

    touch /.metadata-never-index

## Plugins

Installed plugins for Spotlight - macOS know many UTIs - these are additional.

    mdimport -L

Run mddiagnose for DEBUG.

## Show log

    log stream --predicate '(process == "mdworker" OR process == "mdworker_shared")' --info
    log stream --predicate '(process == "mds_stores" OR process == "mdsync" OR process == "mdwrite")' --info
