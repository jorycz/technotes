# macOS Spotlight

## DB Reset

Run as owner for network homes, otherwise run as root.

    mdutil   ### To show options ...

Disable && Reset - under root

    sudo su -
    mdutil -a -i off
    mdutil -aE
    rm -rf /System/Volumes/Data/.Spotlight-V100/
    exit

Delete users's Spotlight now

    rm -rf Library/Metadata/CoreSpotlight/
    pkill mds
    pkill mds_stores
    pkill mdbulkimport

Now REBOOT...  
  
Enable & show status

    sudo su -
    mdutil -a -i on
    mdutil -asv

## Do NOT index HDD

    touch /.metadata-never-index

## Plugins

Installed plugins for Spotlight - macOS know many UTIs - these are additional.

    mdimport -L

Run mddiagnose for DEBUG.

## Show log

    log stream --predicate '(process == "mdworker" OR process == "mdworker_shared")' --info
    log stream --predicate '(process == "mds_stores" OR process == "mdsync" OR process == "mdwrite")' --info
