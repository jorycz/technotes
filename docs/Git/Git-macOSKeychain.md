# Git macOS keychain

## Credentials in macOS Keychain for git

List credentials in macOS Keychain

    # List first record for github.com domain
    printf "protocol=https\nhost=github.com\n" | git credential-osxkeychain get
    # List record for usernaname myuser
    printf "protocol=https\nhost=github.com\nusername=myuser\n" | git credential-osxkeychain get

Clone repository using particular credentials

    git clone https://myuser@github.com/myuser/REPO.git

## Remove GIT username and password (PAT) for HTTPS

    printf "protocol=https\nhost=github.com\nusername=myuser\n" | git credential-osxkeychain erase

And press Enter ...

    git push origin main   ### to login with new credentials

