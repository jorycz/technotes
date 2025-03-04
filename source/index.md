# Welcome to Tech Notes

This is part of my external memory.  
I have commands, how-to guides or code/script snippets stored here.  
It should be working on Linux or macOS.
  
**! WARNING !** <font color="red">Do not copy & execute snippets blindly. You can damage your system.</font>

## My public repository list

[https://github.com/jorycz](https://github.com/jorycz?tab=repositories )

## Show my public IP

    curl checkip.amazonaws.com

* Use `--noproxy '*'` or `--proxy ${HTTP_PROXY}` for curl if needed or [showmyip.com](https://www.showmyip.com)

## Show top with processes filtered with pattern

Pattern is matched against full command line.

    top -d 1 -c -p $(pgrep -d',' -f postgres)
