# Git Common

    - v = verbose

## pull, add all (including delete), commit, push

    git fetch -v   ### updates your local reference to the main branch from origin
    git merge      ### merge changes for the current branch
        ### or git pull which is equivalent of both
    git add -A     ### add (-A / --all) changes (ADM) for whole repository
    git commit -m "MESSAGE"   ### save changes to local repository, create checkpoint
    git push

One-liner if it's safe

    git pull && git add --all && git commit -am "MESSAGE" && git push

## Fast Helpers

    git log --pretty=oneline
    # git whatchanged
    git diff HASH                    ### diff of file contents
    git diff --name-status HASH      ### diff of file names
    git show -C HASH
    git clone https://github.com/jorycz/macOS-setup
    # git clone git@github.com:jorycz/macOS-setup

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

## Stash

    git add folder/file.yml    ### Store only one file
    git add -A                 ### Store ALL actual work

    git stash push -m "STASH MESSAGE"
    git stash list             ### List all stored stashes

        ### Restore _latest_ stash and remove it from stashed (previously stored)
    git stash pop
        ### OR
    git stash pop stash@{X}    ### for particular
        ### OR
    git stash drop <STASH>     ### if you want your work discard
    git stash apply <STASH>    ### if you want your work apply to current branch

## Branches

    git branch -a   ### Show Local and Remote branches

Create and work on new branch

    git switch -c abc                  ### Create & switch to branch abc
    touch file-in-working-branch.txt   ### Work on abc branch ...
    git add -A
    git status
    git commit -am "MESSSAGE"

Push branch to remote

    git switch abc                       ### first time
    git push --set-upstream origin abc   ### later use just git push (in branch)

Pull branch

    git pull   ### first time on OTHER computers
    git switch abc
    git branch --set-upstream-to=origin/abc abc   ### later git pull (in branch)

Merge branch

    git switch main          ### Switch to main or other branch you want to merge with
    git merge abc            ### Merge changes from abc branch to main
    git push                 ### Push to remote
    git branch -D abc        ### Delete branch on local if you wish
    git push -d origin abc   ### Delete branch on remote if you wish

        ### On OTHER computers, refresh changes and delete also
    git fetch --all --prune
    git branch -D abc

* `-d OR --delete` - deletes the branch if it has already been fully merged
* `-D OR --delete --force` - deletes the branch "irrespective of its merged status"

## Undo last Commit

Undo the last commit **but keep changes staged**.

    git reset --soft HEAD~1

Undo the last commit **and unstage changes**, keeping files as `Modified` in `working copy`.

    git reset --mixed HEAD~1

Undo the last commit **and discard (permanently delete) all your changes**. <font color="red">DANGER</font> 

    git reset --hard HEAD~1

## Rebase

If you want to combine local commits before pushing them upstream, for example, because you discovered a typo or bug after a commit, you can do this interactively.

    git rebase -i

## Clone repo and set state to particular commit hash

    git clone https://github.com/<USER>/<REPO> repository-tmp
    cd repository-tmp
    git reset --hard 1234567XYZ5554a093c9e883ce239cf123456789
    ### HEAD is not at ... (commit message)

## Forget my changes on file

    git checkout ABC/abc.info

* If `ABC/abc.info` **is a file path**, this command restores that file from the latest committed version in the current branch, **discarding any local changes in that file**.
* * Beware that if `ABC/abc.info` exists as a **branch**, git will switch to that branch!

## Git Tagging

    git describe --tags

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
