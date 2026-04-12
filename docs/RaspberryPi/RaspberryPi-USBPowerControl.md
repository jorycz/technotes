# Raspberry Pi - USB Power Control

Reason for this solution are ESP32 cameras connected to Raspberry Pi USB ports that sometimes needs restart. RPi 1 is sufficient for this.

## Installation

[uhubctl on github](https://github.com/mvp/uhubctl)

    apt install -y uhubctl

## Usage

cycle_usb_power.sh - script for cycle USB power

    sleep 1
    date
    echo "Disabling USB Power ..."
    echo '1-1' | tee /sys/bus/usb/drivers/usb/unbind
    echo "Disabled."
    sleep 1
    echo "Enabling USB Power ..."
    echo '1-1' | tee /sys/bus/usb/drivers/usb/bind
    echo "Enabled."
    echo

restart_network.sh - in case WiFi is not working after power cycle

    echo restart-wifi-go
    nmcli radio wifi off && sleep 2 && nmcli radio wifi on
    echo restart-wifi-done
    sleep 2
    echo restart-networkd-go
    systemctl restart systemd-networkd
    echo restart-networkd-done
    sleep 2
    echo restart-manager-go
    systemctl restart NetworkManager
    echo restart-manager-done

Helper aliases in ~/.bashrc

    alias s='sudo -i'
    alias c='sudo nohup ./cycle_usb_power.sh &'
    alias r='sudo nohup ./restart_network.sh &'

