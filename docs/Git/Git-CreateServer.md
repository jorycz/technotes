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

Add name of new repository to `gitolite-admin` repository conf file `conf/gitolite.conf` and commit and push. Then clone of new repo from gitolite server.

### If you damage / remove / loose access to server as admin

[lost-admin-keyaccess](https://gitolite.com/gitolite/emergencies#lost-admin-keyaccess)

* Replace current `admin.pub` in gitolite-admin repo with command `gitolite setup -pk admin.pub`

### Custom update script - post info to Google Chat

~/.gitolite/hooks/common/update - this file is rewritten when new repository is added. Add `MY SCRIPT` section again.

    #!/usr/bin/perl

    use strict;
    use warnings;
    use lib $ENV{GL_LIBDIR};
    use Gitolite::Hooks::Update;

    # gitolite update hook
    # ----------------------------------------------------------------------

    #### MY SCRIPT ###
    # $ARGV[2] - actual new commit
    system("/bin/bash ~/bin/sendMsgToGoogleChat.sh $ARGV[2]");
    ##################

    update();               # is not expected to return
    exit 1;                 # so if it does, something is wrong

~/bin/sendMsgToGoogleChat.sh

    #!/bin/bash

    PR="${PROXY}:${PORT}"

    sendMsg() {
        echo -n "Message to Google Chat: "
        # Channel_1
        curl -x ${PR} -s -H "Content-type: application/json; charset=UTF-8" --data "{'text':'$(</dev/stdin)'}" "https://chat.googleapis.com/v1/spaces/AAAAspZSqTM/messages?key=AIzaSyDdI...&token=CnjcBE...%3D" | jq '.text'
    }

    # COMMIT="${1}"
    # COMMIT_SHORT=$(git show -s --format=%h ${1})
    # AUTHOR=$(git --no-pager show -s --format='%an <%ae>' ${COMMIT})
    # COMMENT=$(git show -s --format=%B ${COMMIT})

    MSG=$(git --no-pager show -s --pretty="\`NEW PUSH to GIT [ $GL_REPO ] from [ %cn ] hash [ %h ]\`\n\`%s\`" -C ${1})

    ### Do NOT send mesage to chat for this REPO
    if [ "${GL_REPO}x" == "private-repo-name1" ] ; then exit 0 ; fi

    ### Do NOT send mesage to chat for CRL auto update
    if echo "${MSG}" | grep 'CRL Updater' ; then exit 0 ; fi

    ### SEND NOTIFICATION:
    removed_quotes="${MSG//\'/}"   # single quote ' in string is issue, notification does not work then
    # echo "${removed_quotes} ||| ${1}" > /dev/shm/gitPushTempLog.msg
    echo "${removed_quotes}" | sendMsg 2>&1 > /dev/shm/gitPushMessageDebug

## Restore git repository to server from encrypted backup

* To authorized_keys of git user, add key of user (me) that will be restoring that repository.
* Unpack repository from backup to home of git user
* Then test clone of that repository by `git clone ssh://USER@HOSTNAME/home/USER/REPO.git`

