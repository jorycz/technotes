# macOS - Virtualization

## VMware Fusion

    brew install vmware-fusion

* or install from [VMware / Broadcom Page](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)

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
