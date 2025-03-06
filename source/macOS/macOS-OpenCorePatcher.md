# macOS OpenCore Patcher

## Installation to HDD with dualboot

* [Backup and Restore Boot Camp on a new mac for free](https://www.benchodroff.com/2017/02/15/backup-and-restore-boot-camp-on-a-new-mac-for-free/)
* * <a href="files/Backup_and_Restore_Boot_Camp_on_a_new_mac_for_free_-_benchodroff.com.pdf" download>Page Backup in PDF</a>

### How to proceed with BootCamp

[Check my comment on reddit](https://www.reddit.com/r/OpenCoreLegacyPatcher/comments/15eh6bp/comment/kw8mbge/)

*Hi, I just installed OC on my 2 2017 iMacs, both with bootcamp. There is no issues at all and one iMac shows OC "EFI Boot" also after install on HDD. 2nd iMac didn't show that boot menu item (when holding Option key during startup), so I fixed it like this: Before you install OC on HDD*

    mount EFI partition (diskutil list, sudo mount ...)
    rename Microsoft folder to Microsoft-BAK and delete Boot/boot folder
        (or move it somewhere to USB Flash. I didn't need it later.)
    umount EFI partition using diskutil
    Install OC on HDD and remove USB Flash
    Reset PRAM
    Reboot with Option hey holding and select OC EFI Boot and then macOS Sonoma
    mount EFI, rename MS folder back and unmount, reboot with Option key
    Now naming was wrong, OC EFI Boot name is Windows with OC icon.
        Select it and you can see now proper naming and icons. Select macOS ...
    Set Startup Disk to Macintosh HD or where you Sonoma is

*This way both my iMacs boot to Sonoma automatically. Hope it helps.*

### Installation

If the `EFI Boot` menu item (where the OCP EFI part is) is not displayed when installing on a dual boot HDD (Windows), you need to move the `Boot/boot` directory (delete) in the EFI partition (under the `EFI/` directory) and rename the `Microsoft` directory to `Microsoft-BAK`.  
Afterwards, the OCP installation into EFI on the HDD should go through fine and during boot the OC `EFI Boot` (or with the Windows name but the OCP icon) should appear even without the USB Flash (which we have to remove before restarting).  
Then Shutdown with PRAM reset (option command P R and Startup button)

### Restore Microsoft Windows in the EFI menu

Just remount the EFI partition after booting macOS and rename `Microsoft-BAK` to `Microsoft`.  
In the EFI menu (Hold **Option key** before chime boot sound) it will say Windows, but the icon will be OCP (OpenCore Patcher).  
MacOS and Windows can be started after selecting the OCP icon (now named Windows) :)

### Boot into macOS - if not selected automatically

Just set the **Startup Disk** to `Macintosh HD`.

### Recovery

[https://dcg-ipod.affinityco.net/blogs/help-2/kb-60604-reinstalling-macos-on-a-device-with-opencore](https://dcg-ipod.affinityco.net/blogs/help-2/kb-60604-reinstalling-macos-on-a-device-with-opencore)  
Follow the steps in the app. You will need to:

* Download a copy of the macOS installer. You can choose from a list of different versions - it is recommended to use the same version that you have currently installed. Check which version you have by viewing `About this Mac` in the Apple menu.
* Copy the macOS installer to a USB drive
* Install OpenCore to the USB drive

If you can't follow these steps on your Mac because you can't use it, then you will need to use another Macintosh computer to do so. We recommend preparing a Recovery USB drive ahead of time - make it a part of your backup plan for your data.

To use your Recovery drive, turn off your Mac. Then:

* Turn on your Mac whilst holding cmd + opt + P + R until you hear the chime twice
* Hold opt until you see a menu appear
* Choose OpenCore / EFI from the menu
* Choose Install macOS from the next menu that appears
