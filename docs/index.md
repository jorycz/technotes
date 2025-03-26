# Welcome to Tech Notes - My external memory

I store here commands, how-to guides or code/script snippets. It should be working on Linux or macOS.
  
**! WARNING !** <font color="red">Do not copy & execute snippets blindly. You can damage your system.</font>

* [My Home Page](https://www.rysanci.cz)
* [Public repository list on github](https://github.com/jorycz?tab=repositories)

## Show my public IP

    curl checkip.amazonaws.com

* Or use [showmyip.com](https://www.showmyip.com)

## Generate random password

    openssl rand -base64 6

## Show top with processes filtered with pattern

Pattern is matched against full command line.

    top -d 1 -c -p $(pgrep -d',' -f postgres)

## Deploy by GitHub Action workflow

[GitHub Pages on Material for MkDocs](https://squidfunk.github.io/mkdocs-material/publishing-your-site/)

* Setup in repository `Settings` > `Pages` > `Build and deployment`
* * Source > `Deploy from a branch`
* * Branch > `gh-pages` and `/ (root)`
