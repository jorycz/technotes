# Go golang

## Installation of latest version in shell

First time setup

    echo "export PATH=/usr/local/go/bin:${PATH}" | sudo tee -a $HOME/.bashrc
    echo "export GOPATH=${HOME}/go" | sudo tee -a $HOME/.bashrc
    source $HOME/.bashrc

Set platform `linux-amd64` Linux Intel or `darwin-arm64` macOS Arm

    P="linux-amd64"
    WEBPAGE="https://golang.google.cn/dl/"
    URL=$(echo -n ${WEBPAGE} ; curl -s ${WEBPAGE} | grep 'download downloadBox' | sed "s;.*\/dl\/\(.*\)\".*;\1;" | grep ${P})
    echo "Downloading : $URL"
    curl -s -O -L "${URL}"
    echo "Unpacking ..."
    sudo tar -C /usr/local -xzf *.tar.gz

Check version

    go version
