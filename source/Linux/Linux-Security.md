# Linux Security stuff

## SSH Key upgrade to ED25519

Ed25519 is the most recommended public-key algorithm available today.  
To check all available SSH keys on your computer:

    for i in ~/.ssh/id_*; do ssh-keygen -l -f "${i}"; done | uniq

Generate new key:

    ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519 -C "user@email.info"

If you’re using macOS Sierra 10.12.2 or later, to load the keys automatically and store the passphrases in the Keychain, you need to configure your ~/.ssh/config file:

    Host *
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519
    IdentityFile ~/.ssh/id_rsa   ### If you want to keep old key

Add the private-key to the SSH agent if you are using:

    eval "$(ssh-agent -s)"   ### running ?
    ssh-add -K ~/.ssh/id_ed25519

## Add password to GRUB

Generate password with

    grub-mkpasswd-pbkdf2

On the END of the `/etc/grub.d/00_header` add password like this

    cat << EOF
    set superusers="${USER}"
    password_pbkdf2 ${USER} grub.pbkdf2.sha512.10000.4BFE57123A87...1AKJBS4SA3A5FA....
    EOF

Allow boot without password. Password will be only for GRUB edit. Add `--unrestricted` do obou menuentry v `/etc/grub.d/10_linux` (update of OS can change this file and remove it so check it after update)

    [...]
    echo "menuentry '$(echo "$title" | grub_quote)' --unrestricted ${CLASS} \$menuentry_id_option 'gnulinux-$version-$type-$boot_device_id' {" | sed "s/^/$submenu_indentation/"
    else
    echo "menuentry '$(echo "$os" | grub_quote)' --unrestricted ${CLASS} \$menuentry_id_option 'gnulinux-simple-$boot_device_id' {" | sed "s/^/$submenu_indentation/"
    [...]

