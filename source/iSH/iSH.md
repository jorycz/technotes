# iSH - iOS Linux Shell

## Setup

    apk update
    apk upgrade
    apk add git openssh openssl vim tmux mc shadow cdrkit p7zip
    mkdir $HOME/.ssh
    chmod 700 $HOME/.ssh
    touch $HOME/.ssh/authorized_keys
    chmod 600 $HOME/.ssh/authorized_keys
    touch $HOME/.ssh/id_rsa
    chmod 600 $HOME/.ssh/id_rsa
    touch $HOME/.ssh/id_rsa.pub
    chmod 644 $HOME/.ssh/id_rsa.pub

Change shell to BASH - run `chsh` and enter `/bin/bash`  
Set password for root - run `passwd`

## .profiles

    echo "alias ll='ls -l'" >> ~/.profile
    echo "alias b='~/ish-app/background.sh'" >> ~/.profile

## .tmux.conf

[.tmux.conf](../Linux/Linux-Tmux.md#tmuxconf)

## REBOOT

    exit

## background.sh

    #!/bin/sh

    PCS=$(ps -elf | grep -v grep | grep "cat /dev/location" | awk '{print $1}')

    echo && echo "* Location service for iSH muset be set to ALWAYS in Settings to work." && echo

    if [ -z "$PCS" ]
    then
        # echo "Location background service is NOT active, running NOW ..."
        cat /dev/location > /dev/null &
        echo "iSH app [ BACKGROUND mode is ENABLED ] - app will stay in BACKGROUND ..."
    else
        # echo "Location background service IS active, killing NOW ..."
        kill "$PCS"
        echo "iSH app [ BACKGROUND mode is DISABLED ]. iOS will terminate app later."
    fi
    echo


## Mount Folder on iOS / iPadOS

    mkdir iPad iCloud_Drive idos3
    mount -t ios . iPad/   ### Now choose folder from Files app dialog ...
    ...

## Create ISO image from folder

    apk install cdrkit
    mkdir cd
    ### Copy files ...
    genisoimage -o cd.iso cd/

## 7zip on iOS

    apk add p7zip
    7z x archive.7z
