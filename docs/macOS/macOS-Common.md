# macOS Common

## HDD format / FS delete

    diskutil listFilesystems
    diskutil list
    diskutil unmountDisk /dev/diskX
    diskutil apfs deleteContainer diskX   ### ONLY if diskX is APFS container

       ### FORMAT
    diskutil eraseDisk ExFAT FLASH diskX   ### This will create EFI partition
    diskutil eraseDisk ExFAT FLASH MBR diskX   ### EFI is not created
        ### To DELETE all partitinos ONLY - to have empty HDD without filesystems
    diskutil partitionDisk /dev/diskX MBR Free Space 100%

## ISO / DMG conversion

From ISO to DMG

    hdiutil convert -format UDRW file.iso -o file.dmg

From DMG to ISO

    hdiutil convert -format UDTO file.dmg -o file.iso

## Create installation USB

For macOS

    sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume

For Linux, ISO needs to be converted to DMG format first

    hdiutil convert -format UDRW -o ubuntu-iso.img ubuntu.iso

Write to Flash

    diskutil list
    diskutil unmountDisk /dev/diskX
    sudo dd if=ubuntu-iso.img.dmg of=/dev/rdiskX bs=1m conv=sync
        ### you can see status using Control+T
    diskutil eject /dev/diskX

## Create BOOT USB FLASH Windows ISO

    diskutil list
    diskutil unmountDisk /dev/diskX
    sudo dd if=windows.iso of=/dev/diskX bs=1m
    diskutil eject /dev/diskX

## Repair system volume

{--Run `diskutil repairvolume <diskX>` in Terminal from Recovery Mode.--} Does not work anymore for encrypted volumes.

Now run **Disk Utility** in **Recovery Mode** and repair the disk from there. After that, unmount and mount the disk. Then check if errors are gone and reboot.

## Create ISO from Folder on HDD

Use **Disk Utility** > File > Image from Folder > Browse to and select the folder containing your files  
Set the **Image Format** to DVD/CD master  
Convert CDR to ISO

    hdiutil makehybrid -iso -joliet -o FILENAME.iso FILENAME.cdr

## Create encrypted DMG

    hdiutil create -encryption AES-256 -stdinpass -size 6m -volname "secureStorage" -fs APFS ./secureStorage.dmg
    hdiutil attach ./secureStorage.dmg
    ls -la /Volumes/secureStorage
    hdiutil detach /Volumes/secureStorage/

## FileVault - Check, Validate Recovery Key

It's good to **run this command** from time to time and **for sure when it was changed**.

    sudo fdesetup validaterecovery

## Set Login Screen keyboard layout

    # sudo languagesetup   ### doesn't work anymore
    # # ENG = 1            ### doesn't work anymore
    nvram prev-lang:kbd
    sudo nvram prev-lang:kbd="en_US:0"

Logout / Restart. Should be english keyboard layout on login screen always.  
[https://www.motionbug.com/jamf-connect-filevault-and-the-usa-keyboard/](https://www.motionbug.com/jamf-connect-filevault-and-the-usa-keyboard/)

## Set macOS HTTP Proxy in terminal

    export ALL_PROXY=px:3128

## Flush DNS resolver

    sudo killall -HUP mDNSResponder

## Protect hosts

Set immutable

    sudo chflags uchg /etc/hosts
    sudo chflags schg /etc/hosts

Unset immutable

    sudo chflags nouchg /etc/hosts
    sudo chflags noschg /etc/hosts

## Handy Info Commands

    sw_vers
    sysctl -n hw.model
    sysctl -n hw.memsize
    diskutil info diskX | grep "Media Name"
    diskutil verifyVolume diskX   ### diskXsY ...

## Boot - Startup Keys, Reinstall

Start Built-in macOS Recovery system, NVRAM / PRAM / SMC reset, Safe Mode ...

[Mac startup key combinations](https://support.apple.com/en-gu/102603)
[Reinstall macOS](https://support.apple.com/en-gb/guide/mac-help/mchlp1599/mac)
[Reset the SMC of your Mac](https://support.apple.com/en-us/102605)

## Boot - Verbose Mode

`sudo nvram boot-args` - Show current settings  
`sudo nvram boot-args="-v"` - Enable  
`sudo nvram boot-args=""` - Disable

## Debug Kernel Panic in human readable form

    sudo nvram boot-args="-v keepsyms=y debug=0x144"
    sudo nvram boot-args="-v keepsyms=y debug=0x14e"   ### More verbose

## SMART smartctl

    diskutil list
    diskutil info /dev/diskX | grep SMART      ### Basic info, only if S.M.A.R.T. is supported
    sudo smartctl -l error /dev/disk0          ### Full info if it's supported

## PlistBuddy a defaults

Get BundleID

    defaults read /System/Applications/Utilities/Terminal.app/Contents/Info.plist CFBundleIdentifier

Some Examples

    /usr/libexec/PlistBuddy -c "Print :AppleGlobalTextInputProperties:TextInputGlobalPropertyPerContextInput" ~/Library/Preferences/com.apple.HIToolbox.plist
    /usr/libexec/PlistBuddy -c "Print" ~/Library/Preferences/com.apple.HIToolbox.plist
    /usr/libexec/PlistBuddy -c "Set :AppleGlobalTextInputProperties:TextInputGlobalPropertyPerContextInput true" ~/Library/Preferences/com.apple.HIToolbox.plist
    /usr/libexec/PlistBuddy -c "Add :AppleGlobalTextInputProperties:TextInputGlobalPropertyPerContextInput bool true" ~/Library/Preferences/com.apple.HIToolbox.plist

## Compress to ZIP using random generated password

    openssl rand -base64 6 ; zip -er FILE.zip <folder-OR-file>

## Searching / Filtering logs

    ### Network
    log show --predicate '(processImagePath contains "kernel") && (eventMessage contains "AirPort: Link Down")' --style syslog --last 1d   ### or 5m, 6h ...
    ### Time Machine
    printf '\e[3J' && log stream --predicate 'subsystem == "com.apple.TimeMachine"' --info

## What FILES are accessed by whom

[Talk on SuperUser](https://superuser.com/questions/97980/view-filesystem-access-in-real-time-on-mac-os-x)  
Use `lsof`, `fs_usage`, or `GUI fseventer`  

    sudo fs_usage [PID]

## Disable LOW priority for background processes

    sudo sysctl debug.lowpri_throttle_enabled=0

This will disable low priority for background processes including Time Machine. macOS Could be slower overal.

## List loaded kernel extensions / kext

    sudo kextstat --list-only

## CPU details, power levels, load overal E and P cores

    sudo powermetrics -n 1 -s cpu_power   ### (info since from last boot)

## SWAP on macOS

Turn OFF

    sudo launchctl unload -wF /System/Library/LaunchDaemons/com.apple.dynamic_pager.plist
        ### REBOOT and after reboot: rm /var/vm/swapfile

Turn ON

    sudo launchctl load -wF /System/Library/LaunchDaemons/com.apple.dynamic_pager.plist

Flush

    purge

Show what's using swap right now

    sudo fs_usage | egrep --color=auto "PAGE_|swapfile"

## Czech Spelling

Download dictionary from [OpenOffice](http://ftp.linux.cz/pub/localization/OpenOffice.org/devel/Czech/spell_checking/cs_CZ.zip)  
To `~/Library/Spelling/` copy `cs_CZ.aff`, `cs_CZ.dic` and `hyph_cs_CZ.dic`  
Turn it on in **System Preferences** (Settings) > Keyboard > Text

## Word translation to CZ using Dictionary

Visit [cesky-slovnik-macos](https://github.com/lzap/cesky-slovnik-macos) on github and download [latest version](https://github.com/lzap/cesky-slovnik-macos/archive/refs/heads/main.zip)  
Copy `Czech/objects/Czech.dictionary` to `~/Library/Dictionaries/` (Show ~/Library folder with Command+Shift+L and create Dictionaries folder if not exists)  
In **Dictionary** > Preferences enable `en-cs.txt` and move it UP  

## QuickLook plugins

Folders

* /System/Library/QuickLook/
* /Library/QuickLook/
* ~/Library/QuickLook/

Plugins

* [http://www.quicklookplugins.com](http://www.quicklookplugins.com)
* [Certificate Quicklook](https://code.google.com/archive/p/cert-quicklook/)
* [ZIP viewer](https://macitbetter.com/BetterZip-Quick-Look-Generator/)
* [Image dimension](http://www.cocoaintheshell.com/2012/02/quicklook-images-dimensions/)

Reload of plugin `qlmanage -r` and QL cache `qlmanage -r cache`

## Apple Mail

Disable of showing inline attachments

    defaults write com.apple.mail DisableInlineAttachmentViewing -bool yes

Enable

    defaults delete com.apple.mail DisableInlineAttachmentViewing

## TCP Debug

    sudo lsof -iTCP -sTCP:LISTEN -n -P
    sudo lsof -i TCP -n -P

## Memory compresssion and swap settings

Default is `vm.compressor_mode: 4`  
Memory compressed ON, SWAP OFF `sudo nvram boot-args="vm_compressor=2"`  
Memory compresses OFF, SWAP ON `sudo nvram boot-args="vm_compressor=1"`  
Check after reboot:

* `sysctl -a vm.compressor_mode`
*  `sysctl vm.swapusage`

## SQLite DB View

Autovacuum SQLite /private/var/db/systemstats/snapshots.db  
[Maverick issue](https://discussions.apple.com/message/25482339#25482339)

## Delay Standby mode (OS sleep)

    sudo pmset -a standbydelay 86400   ### 24 hours

## Delay Hibernate after Standby mode

    sudo pmset -a autopoweroffdelay 43200   ### in seconds

## xcrun errors

xcrun: error: invalid active developer path ...  
missing xcrun at: ...

    xcode-select --install

## OOBSOLETE: Sleep to RAM only

    pmset -g   ### to see current settings

Disable hibernate to HDD

    sudo pmset -a hibernatemode 0

Delete sleep image

    sudo rm -f /private/var/vm/sleepimage
    sudo touch /private/var/vm/sleepimage
    sudo chflags uchg /private/var/vm/sleepimage   ### it can not be created now

## OBSOLETE: TRIM

    log show --predicate "processID == 0" | grep spaceman
    log show --predicate "processID == 0" | grep spaceman | grep "trims took"
    log show --start $(date +%F) | grep -i spaceman_scan_free_blocks
    log show --start 2023-02-03 | grep -i spaceman_scan_free_blocks | grep "trims took"
    sudo trimforce enable   ### for non-Apple HDD
    sudo trimforce --enable   ### for non-Apple HDD

## OBSOLETE: NTFS

**This is obsolete and probably not working.**

### Installation

[NTFS-3G](https://github.com/osxfuse/osxfuse/wiki/NTFS-3G)  
[wpbeaches.com](https://wpbeaches.com/how-to-write-to-windows-ntfs-usb-disk-drives-on-macos-macos-mojave-and-sierra/)  

*  Install FUSE for OS X from [https://macfuse.github.io](https://macfuse.github.io) and Homebrew from [https://brew.sh](https://brew.sh)
* * When installing "FUSE for OS X" make sure to select the "MacFUSE Compatibility Layer" option.

    brew install homebrew/fuse/ntfs-3g   ### not working anymore
    brew tap gromgit/homebrew-fuse
    brew install ntfs-3g-mac
    sudo mv /sbin/mount_ntfs /sbin/mount_ntfs.original
    sudo ln -s /usr/local/sbin/mount_ntfs /sbin/mount_ntfs

If using El Capitan, [System Integrity Protection's fs role will first need to be disabled](https://www.reddit.com/r/osx/comments/3hv3kk/update_on_rootless_the_configuration_mechanism/).

### Uninstallation

    sudo nvram boot-args="rootless=0"   ### and REBOOT

    sudo rm /sbin/mount_ntfs
    sudo mv /sbin/mount_ntfs.original /sbin/mount_ntfs
    sudo nvram boot-args="rootless=1"   ### and REBOOT

    brew uninstall ...
