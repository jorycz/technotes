# Git macOS keychain

## Credentials in macOS Keychain for git

Check what type of store is used

    git config --show-origin --get-all credential.helper

Add credentials for **myuser**

    git credential approve <<<"protocol=https
    host=github.com
    username=myuser
    password=github_PAT_token"

List first credentials in macOS Keychain (on Linux it could be stored in ~/.git-credentials)

    printf "protocol=https\nhost=github.com\n" | git credential-osxkeychain get

List credentials in macOS Keychain for **myuser**

    printf "protocol=https\nhost=github.com\nusername=myuser\n" | git credential-osxkeychain get

Clone repository using particular credentials

    git clone https://myuser@github.com/myuser/REPO.git

## Remove GIT username and password (PAT) for HTTPS

    printf "protocol=https\nhost=github.com\nusername=myuser\n" | git credential-osxkeychain erase

And press Enter ...

    git push origin main   ### to login with new credentials

## List all credentials in macOS Keychain related to github.com

    security dump-keychain | grep github.com -A 5 | grep acct

