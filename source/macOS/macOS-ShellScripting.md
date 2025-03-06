# macOS Shell Scripting

## Fill password for sudo in shell script

<font color="red">DANGER! Do not borrow your terminal to anybody.</font> :slight_smile:

Open Keychain Access, select login keychain and use Create New ...

![Keychain Access](files/sudoPassword1.png)

Create alias

![Create](files/sudoPassword2.png)

Use it

    PASS=$(security find-generic-password -l "sudoPassword" -a jr -w | tr -d '\n')
    # -l = name of item in Keychain
    # -a = account name
    # -w = return password only
    echo "$PASS" | sudo -S whoami

Allow `security` utility to access `Keychain` **always** ...
