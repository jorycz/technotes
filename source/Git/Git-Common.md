# Git Common

    - v = verbose

## pull, add all (including delete), commit, push

    git pull
    git add --all
    git commit -am "MESSAGE"
    git push
    # git pull && git add --all && git commit -am "MESSAGE" && git push   ### one-liner

## Fast Helpers

    git log --pretty=oneline
    # git whatchanged
    git diff HASH
    git show -C HASH
    git clone https://github.com/jorycz/macOS-setup
    git clone git@${SERVER}:ansible-cmd

* [Viewing the Commit History on git-scm.com/book](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History)

## Shows differences between remote and local repo (local view)

*Your branch is ahead of 'origin/master' by 2 commits.*

    git diff origin/$(git rev-parse --abbrev-ref HEAD)

Or use these commads:

        ### ALWAYS update remote copy on local
    git remote -v update

        ### what was changed on remote
    git status -uno
        ### only file names with modification type
    git diff --name-status HEAD..origin/$(git branch --show-current)
        ### what was changed on local
    git diff origin/$(git branch --show-current)
        ### only file names with modification type
    git diff --name-status origin/$(git branch --show-current)

## Branches

        ### Show Local and Remote branches
    git branch -a

Create and work on new branch

        ### Create & switch to branch abc (-c = create)
    git switch -c abc
        ### Work on abc branch
    touch file-in-working-branch.txt
        ### ...
    git add --all
    git status
    git commit -am "MESSSAGE"

Push branch to remote

        ### first time
    git switch abc
    git push --set-upstream origin abc
        ### later git push (when in branch)

Pull branch

        ### first time on OTHER computers
    git pull
    git switch abc
    git branch --set-upstream-to=origin/abc abc
        ### later git pull (when in branch)

Merge branch

        ### Switch to main, master or any other branch you want to merge with
    git switch main
        ### Merge changes from abc branch to main
    git merge abc
        ### Push to remote
    git push
        ### Delete branch if you wish on local and remote
    git branch -D abc
    git push -d origin abc
        ### On OTHER computers, refresh changes and delete also
    git fetch --all --prune
    git branch -D abc

* `-d OR --delete` - deletes the branch if it has already been fully merged
* `-D OR --delete --force` - deletes the branch "irrespective of its merged status"

## Discard last commit that hasn't been pushed yet

Undo the commit and unstage the changes, but still keep them. Files will appear as `Modified`

    git reset --mixed HEAD~1

Undo the commit and delete all changes. <font color="red">DANGER</font> This will permanently delete your changes!

    git reset --hard HEAD~1

## Rebase

If you want to combine local commits before pushing them upstream, for example, because you discovered a typo or bug after a commit, you can do this interactively.

    git rebase -i

## Clone repo and set state to particular commit hash

    git clone https://github.com/<USER>/<REPO> repository-tmp
    cd repository-tmp
    git reset --hard 1234567XYZ5554a093c9e883ce239cf123456789
    ### HEAD is not at ... (commit message)

## Git Tagging

    git describe –tags

### Create tag

Workflow is ... add, commit, push ...

    git tag
    git tag 0.0.X
    git push origin 0.0.X   ### after last commit
    git push origin --tags  ### push all created tags

### Delete tag

    git tag -d 0.0.1                   ### delete tag on local
    git push origin :refs/tags/0.0.1   ### delete tag on remote

`git tag 0.0.1` - lightweight - just a pointer to a specific commit
`git tag -a v1.4 -m "my version 1.4"` - annotated - full objects in the Git database, They’re checksummed; contain the tagger name, email, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG)

## Stash - save work for later

        ### Save actual work with new files
    git add .
    git stash push -m "Saving work for later on this branch"
        ### List all
    git stash list
        ### Restore latest stash and remove from stashed (previously saved, repeat for all)
    git stash pop
        ### OR git stash pop stash@{X}   ### for particular
        ### Finally
    git stash drop <STASH>   ### if you want your work discard
    git stash apply <STASH>  ### if you want your work apply to current branch

## Forget my changes on file OR swith to another branch

    git checkout ABC/abc.info

* If ABC/abc.info exists as a **branch**, Git switches to that branch.
* If ABC/abc.info **is a file path**, this command restores that file from the latest committed version in the current branch, **discarding any local changes in that file**.

## Config

    git config --global user.name "MAME"
    git config --global user.email email@somewhere.com
    git config --global pull.rebase false
    # git config --global http.proxy http://proxy:port
    # git config --global http.sslverify false

## Change remote repo URL

    git remote add origin https://github.com/<USER>/<REPO>.git
    # or SSH: git remote <add|set-url> origin git@github.com:<USER>/<REPO>.git
    # or SSH: git remote <add|set-url> origin ssh://git@cmd/home/git/ansible-cmd.git

## DEBUG

    export GIT_CURL_VERBOSE=1
    export GIT_TRACE_PACKET=1
