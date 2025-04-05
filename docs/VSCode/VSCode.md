# Visual Studio Code

 * Sign in to GitHub account.

## Open from terminal for folder

To shell profile on Mac add `alias code="open -a 'Visual Studio Code'"`.  
Then open in terminal like `code .`

## Shortcuts

* `CMD + J` - Hide terminal
* `SHIFT + CMD + F` - Search (everywhere)
* `SHIFT + CMD + E` - File explorer
* `SHIFT + CMD + P` - Preferences
* `SHIFT + CMD + O` - Search in code (variables, functions, ...)
* `CTRL + SHIFT + G` - Open Source Control (git)
* `CMD + P` - Search for a file by name, line, symbol ...
* `SHIFT + CMD + B` - Build
* `CMD + B` - Toggle file explorer
* `CMD + D` - Select the next occurrence of the current word for multi-edit
* `SHIFT + CMD + M` - Error in code tab
* `CTRL + R` - Open Recent window + filtered search
* `OPTION + SHIFT + UP/DOWN` - Duplicate the current line

## Extensions

* [vscode-icons](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons)
* [Go](https://marketplace.visualstudio.com/items?itemName=golang.go)
* [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* * [Python Debugger](https://marketplace.visualstudio.com/items?itemName=ms-python.debugpy)
* * [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
* [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)
* * [Remote - SSH: Editing Configuration Files](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh-edit)
* * [Remote Explorer](https://marketplace.visualstudio.com/items?itemName=ms-vscode.remote-explorer)
* [YAML](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml)
* [Ansible](https://marketplace.visualstudio.com/items?itemName=redhat.ansible)
* [Windsurf](https://marketplace.visualstudio.com/items?itemName=Codeium.codeium)

`ansible` and `ansible-lint` is needed.  Install for macOS:

    brew install ansible ansible-lint

* Open Ansible Extension settings - `Command Palette` - fill `Settings`, open `Ansible: Open Ansible Extension Settings`
* * in `Basic Configuration` enter path to Ansible binary: `/usr/local/bin/ansible`

## Go / golang - do not format on type/save

`CMD + SHIFT + P` > `settings.json`  
In case of remote development / Remote SSH choose `Open Remote Settings SSH` ...

    {
        "[go]": {
            "editor.formatOnType": false,
            "editor.formatOnSave": false,
            "editor.codeActionsOnSave": {
                "source.organizeImports": false
            }
        }
    }

## Remote SSH development

* Install Visual Studio Code Remote Development Extension Pack
* Read [Remote Development with VS Code on Mac in 5 simple steps](https://medium.com/macoclock/remote-development-with-vscode-on-mac-in-simple-5-steps-6ae100938d67)
* * In Extensions search for **remote development extension pack** and install
* * In the command pallete (`CMD + SHIFT + P`) select `Remote-SSH: Add New SSH Host...` and use user@hostname ...
* * Select `Remote-SSH: Connect to Host...` from the Command Palette. From the drop-down choose the already added host
* * * You can edit type of the server (Windows, Linux, Mac) in vscode settings (Code > Preferences > Settings) by updating `remote.SSH.remotePlatform` property

## vscode-server on Linux

Home folder is `.vscode-server`  
Stop Server: `CMD + SHIFT + P` > `Kill VS Code Server on Host`

* Sometimes I do it 2x or disconnect and try it again. It will start next time I connect or project is oppened.

    ps -elf | grep -i vscode-server
    kill $(pidof node)

In case HTTP proxy is needed for vscode-server

    export HTTP_PROXY=http://proxy:port
    export HTTPS_PROXY=${HTTP_PROXY}

## Code-Server on WEB

* [code-server on github](https://github.com/coder/code-server)

### Install

This will install DEB package

    curl -fsSL https://code-server.dev/install.sh | sh

Enable & Start code-server

    sudo systemctl enable --now code-server@${USER}

Or run it in foreground

    code-server

### Config

* /home/${USER}/.config/code-server/config.yaml

Warning! This configuration is for local private LAN or with usage of another authentication like required client certificate, because security of code-server is DISABLED using this:

    bind-addr: 0.0.0.0:8888
    # auth: password
    auth: none
    password: <PASSWORD>
    cert: false

### Update

To update code-server, install the new version over the old version using one-liner in **Install** section. All user data is in `~/.local/share/code-server` so they are preserved between installations.  
My way:

    ### snapshot
    ansible-playbook guest-dev.yml --tags updateVSCode
    ### delete snapshot if everything works
