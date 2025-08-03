# Git Create Server

## Pure git server on linux

[Setting Up the Server on git-scm.com/book](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server)

    apt install git
    su - git
    mkdir ansible.git
    cd ansible.git/
    git init --bare

Secure git account

    cat /etc/shells   ### see if git-shell is already in there and install it if not
    which git-shell
    sudo -e /etc/shells   ### add the path to git-shell
    sudo chsh git -s $(which git-shell)   ### set shell for user git
       ### now ssh git@gitserver doesn't work

At this point, users are still able to use SSH port forwarding to access any host the git server is able to reach. If you want to prevent that, you can edit the `authorized_keys` file and prepend the following options to each key you’d like to restrict:

    no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty ssh-rsa AAAAB3NzaC1yc2EAAAADAQA...

## Gitolite

[Gitolite](https://gitolite.com/)

### Install

    ### Create user `git` with home directory `/home/git`
    su - git
    mkdir bin
    echo 'PATH=$PATH:/home/git/bin' >> /home/git/.bashrc
    
    ### Create first admin user pub key gitolite-admin.pub
    git clone https://github.com/sitaramc/gitolite
    gitolite/install -ln
    bin/gitolite setup -pk gitolite-admin.pub

### Create repository and add new user

* Add name of new repository to `gitolite-admin` repository conf file `conf/gitolite.conf` with username from `keydir` and rights.
* Add public key of user that should have access to this repository, for example `keydir/myUser.pub`
* Commit and push.
* After that, clone of this new empty repo from gitolite server.

### If you damage / remove / loose access to server as admin

[lost-admin-keyaccess](https://gitolite.com/gitolite/emergencies#lost-admin-keyaccess)

* Replace current `admin.pub` in gitolite-admin repo with command `gitolite setup -pk admin.pub`

### Custom update script - post info to Google Chat

Create `.gitolite/hooks/common/post-receive` with content

    #!/bin/bash

    PROXY=http://...

    sendMsg() {
        echo -n "Message to Google Chat: "
        curl -x ${PROXY} -s -H "Content-type: application/json; charset=UTF-8" --data "{'text':'$(</dev/stdin)'}" "https://chat.googleapis.com/v1/spaces/SomeSpaceID/messages?key=someGoogleChatSpaceKey..." | jq '.text'
    }

    while read oldrev newrev refname; do

        for commit in $(git rev-list $oldrev..$newrev); do
            MSG=$(git --no-pager show -s --pretty="GIT push [ $GL_REPO ] from [ %cn ] hash [ %h ]%n%s" "$commit")

            ### Do NOT send mesage to chat for this REPOs
            if [[ "${GL_REPO}" =~ "testing-" ]] ; then exit 0 ; fi

            ### SEND NOTIFICATION:
            removed_quotes="${MSG//\'/}"   ### This char ' is issue. Message with this char never arrives.
            echo "${removed_quotes}" | sendMsg 2>&1 > /dev/shm/gitPushMessageDebug
        done

    done

Run

    cd /home/git && bin/gitolite setup

to update already existing repositories. New ones will be created with link to this hook automatically.

## Restore git repository to server from encrypted backup

* To authorized_keys of git user, add key of user (me) that will be restoring that repository.
* Unpack repository from backup to home of git user
* Then test clone of that repository by `git clone ssh://USER@HOSTNAME/home/USER/REPO.git`

