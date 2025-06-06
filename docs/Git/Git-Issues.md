# Git Issues

## clone issue - remote HEAD refers to nonexistent ref

    warning: remote HEAD refers to nonexistent ref, unable to checkout.

Your git client wants to use **master** branch but **main** is used as default (new name for master).

    git log             ### check what is now your default branch
    fatal: your current branch 'master' does not have any commits yet
    git branch -a       ### show all branches
      remotes/origin/main
    git switch main     ### just switch to main branch
    git branch -a       ### show all branches and main is now default

## push issue - RPC failes

    error: RPC failed; HTTP 400 curl 22 The requested URL returned error: 400
    fatal: the remote end hung up unexpectedly

Try to increase POST buffer

    git config --global http.postBuffer 157286400

## push issue - rejected

    ! [rejected]          master -> master (fetch first)
    error: failed to push some refs to 'host:repo'
    hint: Updates were rejected because the remote contains work that you do
    hint: not have locally. This is usually caused by another repository pushing
    hint: to the same ref. You may want to first integrate the remote changes
    hint: (e.g., 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

Pull and push again should resolve it

    git pull && git push

If not, manual merging could be required.

## pull issue - cannot pull with rebase

    error: cannot pull with rebase: You have unstaged changes.
    error: please commit or stash them.

My changed files were changed on remote repository already. I need to compare files and merge them, not overwrite remote version if I have old version!

    $ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
        modified:   ansible/roles/app/abc/tasks/main.yml

    no changes added to commit (use "git add" and/or "git commit -a")

Shows list of files in I need to compare changes

    $ git diff
    diff --git a/ansible/roles/app/abc/tasks/main.yml b/ansible/roles/app/abc/tasks/main.yml
    index 50789228..aa2b5553 100644
    --- a/ansible/roles/app/abc/tasks/main.yml
    +++ b/ansible/roles/app/abc/tasks/main.yml
    @@ -60,7 +60,14 @@
    
    +  ansible.builtin.lineinfile:
    +    path: '{{ haproxy_config }}'
    +    insertafter: '^\tlog\tglobal$'
    +    line: "\tlog-format \"[%t] Connection on port %fp from ip %ci forwarded to %si:%sp with data %[capture.req.hdr(0)]\""
    +
    @@ -68,8 +75,10 @@
        replace: '\1\t{{ item.new }}'
        backup: yes
    with_items: 
    -    - { section: 'defaults', original: 'option\s+httplog', new: 'option\ttcplog' }
    +    - { section: 'defaults', original: 'option\s+httplog', new: '#option\ttcplog' }

I don't want to delete `option tcplog` which should be there. On this computer I have old version of the file. So I will copy lines I want to keep to pasteboard and just throw away my current changes in this file:

    git checkout -- ansible/roles/app/abc/tasks/main.yml
    ### You can discard ALL your changes if you wish with: git reset --hard

Check everything is OK

    $ git status
    On branch master
    Your branch is up to date with 'origin/master'.

    nothing to commit, working tree clean

    $ git pull
    ...

OR <font color="red">DANGER</font> pull what is in remote repository and continue working

    git stash
    git pull --rebase
    git stash pop  # Apply your stashed changes back

OR <font color="red">DANGER</font> keep my changes and push to remote repository

    git add .
    git commit -m "Save work before pulling"
    git pull --rebase

## Remove GIT username and password (PAT) for HTTPS

    echo "url=https://github.com" | git credential reject ### Remove cached credentials
    git push origin main      ### Enter username and password (Personal Acccess Token)

Or if you have `git credential-osxkeychain`, enter this to Terminal

    git credential-osxkeychain erase
    host=github.com
    protocol=https

And press Enter ...
