# tmux

Great notes: [http://lgfang.github.io/mynotes/utils/tmux-intro.html]

```sh
^b + , Rename window
^b + c New window
^b + d Detach session
^b + w List windows
^b + n/p Next/Previous window

tmux new -s <session-name>
tmux ls - list sessions
tmux attach -t <session> - attach to session
 
^b + : - enter command mode
source-file ~/.tmux.conf - reload config file 

Copy mode:
^b + [ - enter *copy* mode
q or enter - exit *copy* mode

Scrolling:
ctrl + u/d - up/down 1/2 page
g - top of buffer
G - bottom of buffer
/ - search forward
? - search backward
^b + ] - paste buffer
:show-buffer - see contents of buffer
:rename-session - rename session
^b + $ - rename session
space - start copy

Panes:
^b + % - split vertical
^b + " - split horizontally
^b + z - toggle maximize/original size (Zoom!)
^b + { - move left
^b + } - move right
^b + cursor - switch to pane
^b + <up>/<down> - resize (hold all keys at the same time)
^b + <left>/<right> - resize (hold all keys at the same time)
^b + ! - convert pane into a window

^b + space - change layout

^b + s - list sessions
^b + ( | ) - navigate between sessions
```
