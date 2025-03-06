# Git New Repository

## Create new repo from local folder and push to github

Create empty repository on github - NO readme, NO gitignore and NO licence

    echo "# Initial commit." >> README.md
    # touch .gitignore
    # copy LICENSE (MIT)

Create repo from local folder

    git init
    git add .
    git commit -m "Initial release."
    git branch -M main
    git remote add origin https://github.com/<USER>/<REPO>.git
    git push --set-upstream origin main
    # or user shorter version git push -u ...
    # git push -uv origin master   ### push local branch master to remote origin
    # git push -uv origin --all    ### push all branches
    git status

