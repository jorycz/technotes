# macOS - Virtualization

## VMware Fusion

    brew install vmware-fusion

* or install from [VMware / Broadcom Page](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)
* For Shared Folder in Ubuntu:
* * mkdir /mnt/hgfs
* * sudo vmhgfs-fuse .host:/ /mnt/hgfs/ -o allow_other -o uid=1000

### Windows 11

#### Installation without MS account, local only.

During installation it will ask for license, continue and then there is a screen that can ask for network driver or login to MS account. Press `Shift + (Fn) F10` and then in command line that will appear run `start ms-cxh:localonly`

* [DirectX Download](https://www.microsoft.com/en-us/download/details.aspx?id=35)

#### Copied or Moved VM issue

If VMware Fusion asks every start of VM.

    cd /Library/Application\ Support/VMware/VMware\ Fusion/Shared
    mv vmInventory vmInventory.save
    ln -s ~/Library/Application\ Support/VMware\ Fusion/vmInventory .

[Check post 44 on VMs constantly asking if I Copied or Moved VM from Ken Feldman](https://community.broadcom.com/vmware-cloud-foundation/discussion/vms-constantly-asking-if-i-copied-or-moved-vm)

### Ubuntu 24 LTS

Install ARM Ubuntu server and then install desktop and VM tools using

    sudo apt install -y ubuntu-desktop
    sudo apt install -y open-vm-tools open-vm-tools-desktop

* [Enable Auto Login](../Linux/Linux-Common.md#enable-auto-login)
* [Disable Screen Auto Lock](../Linux/Linux-Common.md#disable-auto-lock-screen-autolock-screensaver)

### Ubuntu (ARM version for macOS) Autoinstall

Download Ubuntu ISO, for example ubuntu-25.10-live-server-arm64.iso

    brew install xorriso cdrtools
    xorriso -osirrox on -indev ubuntu-25.10-live-server-arm64.iso -extract / iso/
    chmod 755 iso/
    xorriso -indev ubuntu-25.10-live-server-arm64.iso -osirrox on -extract_boot_images iso/
    find iso -type d -exec chmod 755 {} +
    find iso -type f -exec chmod 644 {} +

Find EFI partition

    find iso/ -name "*img"
    ### ./iso/mbr_part2_efi.img ---> Use this for -append_partition parameter when building ISO

Edit GRUB

    vim iso/boot/grub/grub.cfg
    set timeout=3
    ### append on linux line at the end:
    autoinstall ds=nocloud\;s=/cdrom/nocloud/ ---
    ### Example: linux /casper/vmlinuz $cmdline  --- console=tty0 autoinstall ds=nocloud\;s=/cdrom/nocloud/ ---

Create structure for automated installation

    mkdir nocloud
    ### ... create structure for automated installation ... see below
    cp -v nocloud/* iso/nocloud/

Build ISO

    xorriso -as mkisofs -r -V UBUNTU_ARM64 -o ubuntu-autoinstall.iso -J -l -iso-level 3 -partition_offset 16 -append_partition 2 0xef iso/mbr_part2_efi.img -appended_part_as_gpt -iso_mbr_part_type 0x00 -c boot.catalog -e --interval:appended_partition_2:all:: -no-emul-boot iso/

#### Content of nocloud folder

user-data

    #cloud-config
    autoinstall:
    version: 1
    update: no

    identity:
        hostname: ubuntu
        username: test
        password: "$6$RZHzEtPHl.UTTyJ6$pDKw5mZtystrIiV3RlBsU93nLkCN1ShKPH35ppEQtui3E9yfSfr6TQJ5Q30IwKP2GnU6yrUquBcPc8SJc5fYB1"   ### password is "test" hashed with openssl passwd -6

    ssh:
        install-server: yes

    timezone: Europe/Prague
    locale: en_US.UTF-8
    keyboard: 
        layout: us

    packages:
        - software-properties-common
        - open-vm-tools # for vmware hypervisor
        - open-vm-tools-desktop # for vmware hypervisor
        - ansible
        - git
        - vim

    late-commands:
        - lvextend -l +100%FREE $(lvdisplay | grep "LV Path" | head -n 1 | awk '{print $NF}')
        - echo 'test ALL=(ALL) NOPASSWD:ALL' > /target/etc/sudoers.d/test
        - echo 'alias s="sudo -i"' >> /target/etc/bash.bashrc
        - cp /cdrom/nocloud/99-info /target/etc/update-motd.d/99-info
        - chmod 755 /target/etc/update-motd.d/99-info
        - cp /cdrom/nocloud/*.sh /target/root/
        - chmod 755 /target/root/*.sh
        - mkdir -p /target/etc/systemd/system/systemd-networkd-wait-online.service.d
        - cp /cdrom/nocloud/override.conf /target/etc/systemd/system/systemd-networkd-wait-online.service.d/
        - systemctl daemon-reload


    storage:
        layout:
        name: lvm
        match:
            size: largest

meta-data

    instance-id: ubuntu-autoinstall
    local-hostname: ubuntu

override.conf

    [Service]
    TimeoutStartSec=5

99-info

    #!/bin/sh

    printf "\n===========================================\n"
    printf "s for sudo to root\n"
    printf "ls *.sh\n"
    printf "===========================================\n\n"

run.sh

    #!/bin/bash

    apt -y dist-upgrade
    apt install -y ubuntu-desktop

    echo
    echo "Enable AutomaticLogin in /etc/gdm3/custom.conf"
    echo

mount.sh

    #!/bin/bash

    mkdir -p /mnt/hgfs &> /dev/null
    vmhgfs-fuse .host:/ /mnt/hgfs/ -o allow_other -o uid=1000

