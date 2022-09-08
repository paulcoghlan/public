# vim

## Meta

u - Undo
CTRL+r - redo
. - repeat last command

## Search

```sh
/<string> - search forward
?<string> - search backward
Search and Replace:
Every occurrence in file:              :%s/OLD/NEW/g
n - repeat last search
```

## Movement

```sh
0 - move to start of line
i  - insert at point
A - to append to end of line
HML - move to top, middle or bottom of screen
hjkl - move L U D R
$ - move to end of line
wb - move forward/backward by word
e - move to end of word

1G - move to start of file
G - move to end of file
w - move forward by word
b - move back by word
cw - change to end of word
c$ - change to end of line
ciw - change word at current cursor position (more useful!)

{ - move to start of paragraph/code-block
} - move to end of paragraph/code-block

t<char> - move to next occurence of char
T<char> - move to previous occurence of char

* - find all matches of word where cursor is landed
# - find all matches of word where cursor is landed

CTRL+E - scroll window down
CTRL+Y - scroll window up
CTRL+F - scroll down 1 page
CTRL+B - scroll up 1 page
```

## Copy/Paste

```sh
yiw - copy word at current cursor position
viwp - paste word at current cursor position

dd - delete line
d$ - delete to end of line
:1,.d - delete from start of file to current line
:.,$d - delete from current line to end of file
dG - delete to end of file
```

## Files

```sh
:e FILENAME - edit filename
:r FILENAME - insert contents of filename
:r !<cmd> - put contents of command
:w FILENAME - save as filename

!<cmd> - run command
```

## Visual Mode

```sh
v - visual mode - start selection - then use hjkl to move, y to copy or d to cut/delete, p to paste
CTRL+v - to select rectangular blocks!
```

## Registers

Use " prefix to access registers
"" is the unnamed register you use with yank/paste
"a to "z are the named registers
"0 to "9 are the numbered registers (managed automatically?)
:reg lists registers
Store line to a register: "ayy
Paste line from register: "ap

## Ripgrep

g - ripgrep search
:[num]cn - first result
:[num]cp - prev result
:cfir - first result
:clas - last result

## Macros

q to record a macro; @ to execute it again
1G to go to the top of the file, G to go to the bottom

## Windows

```sh
CTRL+W W - change window

CTRL+W v - split window vertically
CTRL+W s - split window horizontally
CTRL+W _ - maximise current window 
CTRL+W = - equalise windows 
CTRL+W + - increase window size
CTRL+W - - decrease window size
```

## Buffers

```sh
CTRL+O - go back

CTRL+] - follow linkuffers - list buffers
buffer <n> - goto buffer
:b <tab> - goto previous buffer with tab completion for name
:b# - visit last file
CTRL+^ - visit last file
```

## Tabs

SHIFT+H - next tab
SHIFT+L - previous tab

## Tricks

Add JSON formatting to current buffer:

:%!jq '.'

Edit file in hex:
:%!xxd

Save file in hex:
:%!xxd -r

Disable line numbering:
:set norelativenumber
:set nonumber

Show non-ascii characters:
/[^\x00-\x7F]

Show line endings:
:set list

Long command line editing:
[https://nuclearsquid.com/writings/edit-long-commands/]
Use Ctrl-x Ctrl-e to edit current line

## FZF

Install FZF: [https://github.com/alok/notational-fzf-vim]

```sh
c-x: Use search string as filename and open in vertical split.
c-v: Open in vertical split
c-s: Open in horizontal split
c-t: Open in new tab
c-y: Yank the selected filenames
<Enter>: Open highlighted search result in current buffer
```

## Status line

Status Line Generator: [http://tdaly.co.uk/projects/vim-statusline-generator/]

## Markdown

[https://github.com/plasticboy/vim-markdown]

```sh
zR: opens all folds
zm: increases fold level throughout the buffer
zM: folds everything all the way
za: open a fold your cursor is on
zA: open a fold your cursor is on recursively
zc: close a fold your cursor is on
zC: close a fold your cursor is on recursively
gx: open the link under the cursor in the same browser as the standard gx command. <Plug>Markdown_OpenUrlUnderCursor
ge: open the link under the cursor in Vim for editing. Useful for relative markdown links. <Plug>Markdown_EditUrlUnderCursor
The rules for the cursor position are the same as the gx command.
]]: go to next header. <Plug>Markdown_MoveToNextHeader
[[: go to previous header. Contrast with ]c. <Plug>Markdown_MoveToPreviousHeader
][: go to next sibling header if any. <Plug>Markdown_MoveToNextSiblingHeader
[]: go to previous sibling header if any. <Plug>Markdown_MoveToPreviousSiblingHeader
]c: go to Current header. <Plug>Markdown_MoveToCurHeader
]u: go to parent header (Up). <Plug>Markdown_MoveToParentHeader
```
