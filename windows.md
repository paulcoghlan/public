# Windows 11

## Windows Installation

Show hidden files and file extensions in Windows Explorer
Right-click sidebar -> Taskbar settings -> Disable Widgets, Search, Task View

## SSH 

Install from Apps > Optional Features
`C:\ProgramData\ssh` is hidden but has SSH server config

Set default shell to WSL2:
`New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\WINDOWS\System32\bash.exe" -PropertyType String -Force`

Use to set default distro:
`wsl --set-default DISTRO`

View logs from Event Viewer > Applications & Services Logs > OpenSSH

## Apple UK Keyboard ISO

1. Sharp Keys to swap ` and \ keys
2. ~Power Toys for CTRL+C/CTRL+V to CMD+C/CMD+V mapping~ too much hassle as CMD+C/V combinations get
swallowed by apps
3. US layout to get # keys

## Docker/Ubuntu Installation

1. Copy over SSH keys
2. Checkout github repos

## Desktop

Use English US Keyboard for SHIFT+3 (#) key
Win + <- or ->: Snap window to left or right of screen
F11 - Full screen 
Win + up - Full screen
CTRL + Win + < or > - Switch Virtual Desktop
ALT + Tab - Show all Virtual Desktops 

## Terminal

CTRL + TAB - Next Tab
CTRL + SHIFT + TAB - Previous Tab

## WSL

List distros:

- `wsl -l` list distros
- `wsl --set-default <distro name>`
- `wsl --unregister <distro name>`

## Bitwarden

Use Bitwarden CLI from Ubuntu:

```sh
bw unlock
...  
export BW_SESSION="<somekey>"

bw list items | jq '.'
```
