# VirtualBox

## Installation

* Download [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* Download [Debian Installer](https://www.debian.org/devel/debian-installer/)
* Create new VM and set Debian Installer image as CDROM
* Set Boot from CD
* Install system, set proxy/no-proxy, ...

## Export of VM to new VirtualBox installation

    VBoxManage list vms
    VBoxManage export "Win 10" -o ~/tmp/Win10.ova
    VBoxManage import ...

## Shrink HDD

Convert VMDK to VDI, compact VDI and then convert VDI to VMDK.

    #!/bin/bash

    VMDK="win10-disk1.vmdk"
    VMDKTMP="win10-disk1_tmp.vmdk"
    VDI="temp.vdi"

    VBoxManage showhdinfo "$VMDK" | grep -i uuid

    echo
    echo "Can you see disk (first) UUID? Can we continue?"
    read

    echo
    echo "Converting to VDI ..."
    VBoxManage clonehd "$VMDK" "$VDI" --format vdi

    echo "Compacting ..."
    VBoxManage modifyhd "$VDI" --compact
    
    echo "Converting to VMDK ... delete [ $VMDK ] later!"
    # rm "$VMDK"
    VBoxManage clonehd "$VDI" "$VMDKTMP" --format vmdk

    echo
    echo "What was UUID for original VMDK disk? :"
    read UU

    VBoxManage internalcommands sethduuid "./$VMDKTMP" "$UU"

    if mv "$VMDKTMP" "$VMDK"
    then
        ls -l
        echo
        echo "Delete $VDI ?"
        read
        rm "$VDI"
        echo "Delete [ $VMDK ] manually ..."
    else
        echo "ERROR ! Check terminal output."
    fi

    ls -l
