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

## Restore git repository to server from encrypted backup

* To authorized_keys of git user, add key of user (me) that will be restoring that repository.
* Unpack repository from backup to home of git user
* Then test clone of that repository by `git clone ssh://USER@HOSTNAME/home/USER/REPO.git`
