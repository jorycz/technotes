# Linux TMUX

## .tmux.conf

screen user here ... ~/.tmux.conf

    unbind C-b
    set -g prefix C-a
    bind C-a send-prefix
    bind-key C-a last-window

## Sessions

* Connect to first available `tmux a`
* List `tmux ls`

Output

    0: 1 windows (created Tue May 17 18:48:13 2011) [80x23]
    5: 4 windows (created Tue May 17 18:55:09 2011) [80x23] (attached)

* Connect to sesion 5 `tmux a -t 5`

## Basic control after remap to screen-like behaviour

    Ctrl-a c   new window
    Ctrl-a n   next window
    Ctrl-a p   previous window
    Ctrl-a ,   rename window
    Ctrl-a w   show list of all windows
    Ctrl-a f   find window
    Ctrl-a :   switch to command mode
    Ctrl-a d   detach

## Work with windows

    Ctrl-a %   split screen horizontally
    Ctrl-a "   split screen vertically
    Ctrl-a o   switch between windows
    Ctrl-a {   move window to left
    Ctrl-a }   move window to right
    Ctrl-a q   show window numbers
    (when numbers are displayed, enter number to switch to that window)

## Advanced control

Prepare session with windows already opened

    #!/bin/bash
    tmux new-session -d -s servers

    tmux new-window -t servers:1 -n 'Server1' 'ssh user@1.2.3.4'
    tmux new-window -t servers:2 -n 'Server2' 'ssh user@5.6.7.8'
    tmux new-window -t servers:3 -n 'Server3' 'ssh user@9.1.2.3'

    tmux select-window -t servers:1
    tmux attach-session -t servers

## Open new tmux right after login

* Add `new-session` to `.tmux.conf`
* Add `tmux attach` at the end of `.bash_profile`
