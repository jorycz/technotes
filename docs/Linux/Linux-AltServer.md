# Linux Alt Server

[AltServer-Linux-PyScript](https://github.com/powenn/AltServer-Linux-PyScript)

## Install

Install using [AltServer-Linux script](https://github.com/NyaMisty/AltServer-Linux) it is very easy.  
I need to install requests python module on Ubuntu 24 `apt install python3-requests`

* Map iPad connected to linux CT (using Proxmox)
* * Resources / Add Device Passthrough / DEVICE (like /dev/bus/usb/001/004)

Run CT and on it `usbmuxd` for first pairing, then `python3 main.py`. More details [here](../Linux/Linux-Systemd.md#bash-script-to-unit-altserver-example)
