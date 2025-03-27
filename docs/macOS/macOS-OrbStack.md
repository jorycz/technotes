# macOS OrbStack

* [Download from web](https://orbstack.dev) or use homebrew `brew install orbstack`
* [Linux Distributions](https://docs.orbstack.dev/machines/distros)
* [Linux Machines](https://docs.orbstack.dev/machines/#ssh)
* [Networking / Direct Access](https://docs.orbstack.dev/docker/network#direct-container-access)
* [SSH access and Ansible Setup](https://docs.orbstack.dev/machines/ssh)

## Orb List

    orb list

## Create VM

    orb create --help
    orb create ubuntu VM_NAME
        ### orb create --arch amd64 ubuntu VM_NAME

## Connect to machine

    orb   ### default machine and user will be started / used
    orb -m VM_NAME   ### login to VM_NAME as default user
    orb -m VM_NAME -u root   ### login to VM_NAME as root

## Initial setup

    cd
    echo 'cd' >> .bashrc
    echo 'alias s="sudo su -"' >> .bashrc

Proxy

    echo 'Acquire::http::Proxy "http://${HOST}:3128";' > /etc/apt/apt.conf.d/99proxy

Update and Install

    sudo apt update
    sudo apt dist-upgrade -y 
    sudo apt install -y software-properties-common bash-completion vim git sudo dialog tzdata net-tools curl wget netcat-openbsd rsync
        ### Turn off unattended upgrades
    # echo 'Unattended-Upgrade::Allowed-Origins:: "LP-PPA-mozillateam:${distro_codename}";' | sudo tee /etc/apt/apt.conf.d/51unattended-upgrades-firefox
    sudo apt purge unattended-upgrades

## Clone VM

    orb clone VM_NAME VM_NAME_CLONE

## Delete VM

    orb delete VM_NAME

## Orb Start, Stop, ...

    for i in $(orb list -q) ; do orb stop $i ; done   ### stop ALL VMs
    orb start VM_NAME
    orb stop VM_NAME

## Set default Orb VM

    orb default
    orb default VM_MAME

## Boot logs

    orb logs VM_NAME

## List all commands

    orbctl --help

## Xrdp GUI

    sudo apt install -y xrdp xorgxrdp
    sudo apt install -y xfce4 xfce4-goodies
    echo "xfce4-session" | tee .xsession

### Error on Ubuntu 24.10

In user's home log `.xorgxrdp.10.log` is error

    expected xrdp client_info version 20230425, got 20210723
    (EE) Incompatible xrdp version detected  - please recompile(EE)

[Xrdp and xorgxrdp have been compiled at different versions in Oracular](https://bugs.launchpad.net/ubuntu/+source/xrdp/+bug/2085347)

Can be resolved by early upgrading to plucky from this [reddit](https://www.reddit.com/r/Ubuntu/comments/1hk8xcc/upgrade_to_2504_is_not_working_using/) thread

    wget -c http://archive.ubuntu.com/ubuntu/dists/plucky/main/dist-upgrader-all/current/plucky.tar.gz
    tar zxvf plucky.tar.gz
    ./plucky

## Web Browser

Latest stable Firefox

    sudo add-apt-repository ppa:mozillateam/ppa

Prioritize apt version over snap one

    echo '
    Package: *
    Pin: release o=LP-PPA-mozillateam
    Pin-Priority: 1001

    Package: firefox
    Pin: version 1:1snap*
    Pin-Priority: -1
    ' | sudo tee /etc/apt/preferences.d/mozilla-firefox

Install

    sudo apt install firefox

[Source](https://askubuntu.com/questions/1399383/how-to-install-firefox-as-a-traditional-deb-package-without-snap-in-ubuntu-22/1404401#1404401)

## Data Storage

    du -sh ~/Library/Group\ Containers/HUAQ24HBR6.dev.orbstack/data
