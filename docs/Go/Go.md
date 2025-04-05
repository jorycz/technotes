# Go golang

* [Go Releases](https://go.dev/dl/)

## Installation of latest version in shell

### First time setup

    echo "export PATH=/usr/local/go/bin:${PATH}" | sudo tee -a $HOME/.bashrc
    echo "export GOPATH=${HOME}/go" | sudo tee -a $HOME/.bashrc
    source $HOME/.bashrc

### Install

Set platform `linux-amd64` Linux Intel or `darwin-arm64` macOS Arm

    cd ~/Downloads/
    P="linux-amd64"
    WEBPAGE="https://golang.google.cn/dl/"
    URL=$(echo -n ${WEBPAGE} ; curl -s ${WEBPAGE} | grep 'download downloadBox' | sed "s;.*\/dl\/\(.*\)\".*;\1;" | grep ${P})
    echo "Downloading : $URL"
    curl -s -O -L "${URL}"
    echo "Unpacking ..."
    sudo tar -C /usr/local -xzf *.tar.gz

Check version

    go version

## Update

    cd /usr/local/
    tar -czf /home/${USER}/usr-local-go-$(date -I)-backup.tar.gz go/      ### BACKUP
    sudo rm -rf /usr/local/go/

Now continue in **Install** section

## Create basic module

**goApp** application

    cd ${HOME}/
    mkdir go
    cd go
    mkdir goApp
    cd goApp
    go mod init github.com/USER/goApp
    ### go mod init <module_name>
    ### import "<module_name>/<pkg_name>"
    touch goApp.go

## Add package to module

    cd go/goApp
    mkdir logging
    touch logging/logging.go

in goApp.go insert

    import (
        "github.com/USER/goApp/logging"
    )

[Create a module meant for sharing](https://softchris.github.io/golang-book/03-projects/03-create-shared-module/)

## Import local module

Clone repository of module next to folder of goApp local repo, in which I want to use this module. Then place to `go.mod` below **require**:

    module github.com/USER/tasmota-alerter

    go 1.21.6

    require (
        ...
        github.com/USER/sp-json v0.0.0-20240222204221-22d496d5b142
    )

    replace github.com/USER/sp-json => ../sp-json

    require (
        ...
    )

## Handy commands

    go mod tidy      ### find and download all modules app depends on

