# Git Common

    - v = verbose

## Git commit push oneliner

    git pull && git add --all && git commit -am "MESSAGE" && git push

## Fast Helpers

    git log --pretty=oneline
    # git whatchanged
    git diff HASH
    git show -C HASH
    git clone https://github.com/jorycz/macOS-setup
    # git clone git@cmd:ansible-cmd

* [Viewing the Commit History on git-scm.com/book](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History)

## Shows differences between remote and local repo (local view)

*Your branch is ahead of 'origin/master' by 2 commits.*

    git diff origin/$(git rev-parse --abbrev-ref HEAD)

Or use these commads:

    git remote -v update && git status -uno
    git diff origin/master

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

## Rebase

If you want to combine local commits before pushing them upstream, for example, because you discovered a typo or bug after a commit, you can do this interactively.

    git rebase -i

## Stash - save work for later push or discard

    git stash save "COMMENT"
    git stash list
    git stash drop <STASH>   ### if you want your work discard
    git stash apply <STASH>  ### if you want your work apply to current branch

## Forget my changes on file OR swith to another branch

    git checkout ABC/abc.info

* If ABC/abc.info exists as a **branch**, Git switches to that branch.
* If ABC/abc.info **is a file path**, this command restores that file from the latest committed version in the current branch, **discarding any local changes in that file**.

## Branches

`git branch` - Show **Local** branches
`git branch -r` - Show **Remote** branches

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

## DEBUG

    export GIT_CURL_VERBOSE=1
    export GIT_TRACE_PACKET=1
