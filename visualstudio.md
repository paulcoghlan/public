# Visual Studio

See [https://code.visualstudio.com/docs/getstarted/keybindings]

## Meta

```sh
SHIFT+CMD+P     Command palette
CMD+K CMD+S     See shortcuts

CTRL+TAB        Show history, navigate with CTRL+- CTRL+SHIFT+-
```

F12 -           Goto Definition
CTRL+-          Go back

ALT+R           See Recent Projects
CMD+P           Open File, then '?' to see suggested commands

CMD+,           Open User Settings JSON

## Windows

```sh

SHIFT+CMD+N     New Window
SHIFT+CMD+[     Left Editor
SHIFT+CMD+]     Right Editor
CMD+W           Switch Window

CMD+K Z         Zen Mode followed by ESC ESC to exit
CTRL+CMD+F      Fullscreen mode

SHIFT+CMD+E     Explorer Window
CMD+B           Toggle sidebar
CMD+J           Toggle Debug/Terminal (more useful then CMD+U)

CTRL+`          Show Terminal Window
CTRL+^          Toggle maximized Terminal
SHFT+CMD+[      Previous Terminal
SHFT+CMD+]      Next Terminal
CTRL+CMD+UP     Increase Terminal height
CTRL+CMD+DOWN   Decrease Terminal height
CMD+CLICK       Navigate to source from Terminal output

CMD+E           Focus on Editor
CMD+U           Focus on Debug/Terminal
```

## Editing

```sh
CMD+L           Select line
SHIFT+CMD+L     Select all occurances of current selection to edit at once
ALT+UP          Move line up
ALT+DOWN        Move line down
SHIFT+ALT+UP/DOWN Copy line up/down

CMD+[           Indent less
CMD+]           Indent more
SHFT+CTRL+CMD+<- Reduce selection
SHFT+CTRL+CMD+-> Expand selection

F2              Rename symbol

CMD+\           Split editor 
CMD+'           Move to next split view

or:
ALT+CMD+1       Single window
ALT+CMD+2       Two windows

CMD+W           Close Editor
```

## Code

```sh

SHIFT+ALT+F     Format code
ALT+CMD+[       Code folding
ALT+CMD+]

```

## Debug

```sh
CMD+F1          Focus on debug console (for hotkey stepping)

CMD+; C         Re-run tests at cursor
CMD+; F         Re-run tests in file

```
Go: Generate Interface Stubs

## Search

```sh
SHIFT+CMD+F     Search in Files
F4              Navigate search results 
```

SHIFT+CMD+M     See Errors/Warnings, followed by F8/SHIFT+F8 to navigate

## VIM Plugin

gd - jump to definition.
gh - equivalent to hovering your mouse over wherever the cursor is. Handy for seeing types and error messages without reaching for the mouse!

CMD+D or gb - multi-cursor mode for making multiple edits at once.  Repeat CMD+D for each selection (or use rename symbol (F2))

/  - search in file
n  - next search result
N  - previous search result

Set `"vim.useCtrlKeys": false` to avoid clash with Windows CTRL+V

## markdown

CMD+K + V - Show preview window next to markdown

## Extensions

CTRL+SHIFT+G - Show/hide Git
ALT+G   - Git Commit
CMD+F10 - Open Regexp Tool
