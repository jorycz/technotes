# Linux Rescue

## Tools

* SystemRescueCD
* Imager Tool G4L (EFI version)
* CloneZilla

## Useful info command

    cat /proc/mdstat
    pvdisplay -m
    vgdisplay -A -v
    testdisk /list

## FSCK on LVM

    lvscan

In case of INACTIVE devices, make them ACTIVE if you know what you are doing

    lvchange -ay /dev/vg_root/var
    fsck /dev/vg_root/var

## Rescue

Usually boot from USB and fix MBR, GRUB, ...

* [System Rescue Download](https://www.system-rescue.org/Download/) and [How-Tos on System Rescue Homepage](https://www.system-rescue.org)

Using System Rescue

    root@sysresccd /root % fsarchiver probe simple
    [=====DEVICE=====] [==FILESYS==] [=====LABEL=====] [====SIZE====] [MAJ] [MIN]
    [/dev/sda1       ] [ext3       ] [boot           ] [   256.00 MB] [  8] [  1]
    [/dev/sda2       ] [reiserfs   ] [debian         ] [    16.00 GB] [  8] [  2]
    [/dev/sda3       ] [ntfs       ] [winxp32        ] [    16.00 GB] [  8] [  3]

FS structure

    sda2 /
    sda3 /tmp
    sda4 /home

Check **root FS** - sda2

    root@sysresccd /root % mkdir -p /mnt/linux
    root@sysresccd /root % mount -r /dev/sda2 /mnt/linux
    root@sysresccd /root % ls -l /mnt/linux/sbin/init
    -rwxr-xr-x 1 root root 37384 2008-08-12 15:20 /mnt/linux/sbin/init
    root@sysresccd /root % umount /mnt/linux

Check **boot FS** - if it's on different partition than sda2

    root@sysresccd /root % mkdir -p /mnt/boot
    root@sysresccd /root % mount -r /dev/sda1 /mnt/boot
    root@sysresccd /root % ls -l /mnt/boot
    lrwxrwxrwx 1 root root        1 2008-08-05 22:46 boot -> .
    -rw-r--r-- 1 root root    98203 2009-10-27 10:05 config-2.6.30-bpo.2-amd64
    drwxr-xr-x 2 root root     2800 2009-11-12 19:38 grub
    -rw-r--r-- 1 root root  8198587 2009-11-08 14:59 initrd.img-2.6.30-bpo.2-amd64
    drwx------ 2 root root       48 2006-11-25 15:55 lost+found
    -rw-r--r-- 1 root root  1508757 2009-10-27 10:05 System.map-2.6.30-bpo.2-amd64
    -rw-r--r-- 1 root root  2224064 2009-10-27 10:04 vmlinuz-2.6.30-bpo.2-amd64
    root@sysresccd /root % umount /mnt/boot

GRUB fix - **boot FS is same as root FS** - sda2

    root@sysresccd /root % mkdir /mnt/linux
    root@sysresccd /root % mount /dev/sda2 /mnt/linux
    root@sysresccd /root % mount -o bind /proc /mnt/linux/proc
    root@sysresccd /root % mount -o bind /dev /mnt/linux/dev
    root@sysresccd /root % mount -o bind /sys /mnt/linux/sys
    root@sysresccd /root % chroot /mnt/linux /bin/bash
    /usr/sbin/grub-install /dev/sda
    exit

    root@sysresccd /root % umount /mnt/linux/{dev,proc,sys}
    root@sysresccd /root % umount /mnt/linux/boot
    root@sysresccd /root % umount /mnt/linux/
    reboot

You can use [Debian Netinst iso](https://www.debian.org/CD/netinst/#netinst-stable) for repair.

* Download and [convert to DMG under macOS](../macOS/macOS-Common.md#iso-dmg-conversion)
* Insert USB Flash
* `diskutil list` to see disk number
* `diskutil unmountDisk /dev/diskX` to just unmount
* <font color="red">DANGER</font> `sudo dd if=debian-8.3.0-amd64-netinst.img.dmg of=/dev/rdiskX bs=1m`
* Boot from USB, mount `root fs` (assemble RAID if needed and then ...vg0/root/ ...)
* Install GRUB to both disk in case of RAID (/dev/sda and /dev/sdb)
* * `fsck ...`
* REBOOT
* * `fsck ...`

`/dev/md0` (if it's /boot) can be unmounted always and checked anytime
`/dev/md1` (if it's root LVM) can not be checked anytime - boot from live CD

## Recovery of data on NAS with custom blocksize

All recovery tools were refusing to do anything with that HDD. Only way how I can access it was this

    debugfs
    open -b 65536 /dev/md2
    ls
    cd /path/to/data
    rdump "MyData" /path/to/mounted/2nd_disk
    ########################################################################
       -b blocksize
              Forces the use of the given block size (in bytes) for the file
              system, rather than detecting the correct block size
              automatically.  (This option is rarely needed; it is used
              primarily when the file system is extremely badly
              damaged/corrupted.)
