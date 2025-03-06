# macOS Time Machine

* [sparsebundle, backupdb, etc](https://github.com/0xdevalias/devalias.net/issues/89)

## Searching in logs

Live Stream

    printf '\e[3J' && log stream --predicate 'subsystem == "com.apple.TimeMachine"' --info

Specified date or time windows

    printf '\e[3J' && log show --predicate 'subsystem == "com.apple.TimeMachine"' --info --last 3h
    printf '\e[3J' && log show --predicate 'subsystem == "com.apple.TimeMachine"' --info --start "2024-06-08 01:00:00" --end "2024-06-08 06:00:00"
     | egrep "Starting backup with mode|Backup succeeded|Backup failed"
     | grep -A 1 "Finished copying items for"

Meta data Delta. If it's "Zero KB" - delta backups doesn't work and only way I made it working again was OS reinstall. More on this issue on [reddit](https://www.reddit.com/r/MacOS/comments/1gk4mmq/time_machine_doing_full_backup_again_and_again/)

    | grep " backups:"

## List local snapshots by date

    # tmutil listlocalsnapshots / 
    tmutil listlocalsnapshotdates /

## Delete local snapshots

    tmutil deletelocalsnapshots <DATE.NUMBER>

## DELETE ALL LOCAL SNAPSHOTS

    for i in $(tmutil listlocalsnapshotdates / | grep '[0-9][0-9][0-9][0-9]') ; do echo -n "DELETING $i ..." ; tmutil deletelocalsnapshots $i ; done

## Other Usefull Commands

    tmutil status
    tmutil enable
    tmutil disable
    tmutil startbackup
    tmutil startbackup -d 530BA50F-A553-46C3-A625-70DBABC72C9E
       ### List of backups in current folder on local backup HDD
    tmutil listbackups
       ### List of backup on network backup - mount when starting backup or just Browse Time Machine backups from TM menu ...
    tmutil listbackups -d "/Volumes/Backups of Macbook Air M1"
       ### SETTINGS
    defaults read /Library/Preferences/com.apple.TimeMachine.plist
    defaults read /System/Library/Preferences/Logging/Subsystems/com.apple.TimeMachine.plist

## Set QUOTA

    tmutil destinationinfo
    ====================================================
    Name          : timemachine
    Kind          : Network
    URL           : smb://smbuser@tm._smb._tcp.local./timemachine
    ID            : 530BA50F-A553-46C3-A625-70DBABC72C9E
    Quota         : 380 GB

Now you can use

    ### tmutil setquota <ID> <SIZE_IN_GB>
    tmutil setquota 530BA50F-A553-46C3-A625-70DBABC72C9E 130

## Show Extended attributes on Linux

    apt install attr
    # file: media/TimeMachine-Backup
    getfattr /media/TimeMachine-Backup
    user.org.netatalk.Metadata
    user.org.netatalk.has-Extended-Attributes

If there are no extended attributes, result is EMPTY.
