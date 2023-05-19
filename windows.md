# Windows 11

## Windows Installation

Show hidden files and file extensions in Windows Explorer
Right-click sidebar -> Taskbar settings -> Disable Widgets, Search, Task View
Admin Powershell:
```
winget uninstall "windows web experience pack"
```

## Recovery Drive

0. Create System Restore before upgrades

1. Boot into BIOS
2. Select Boot menu and override with USB drive
3. Choose 'Repair Windows' when USB booted
4. Select 'Troubleshoot' and 'Advanced'
5. Choose 'Uninstall Quality Update'

## System Restore Point

Control Panel > Recovery

'Create a restore point right now'

## SSH 

Copy SSH key to `C:\Users\<user>\.ssh` so that you can use
VSCode Remote Extension to SSH.

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
4. Remove XBox Game Bar to avoid annoying shortcuts using Admin PowerShell: `Get-AppxPackage Microsoft.XboxGamingOverlay | Remove-AppxPackage`
5. In VSCode Windows and Mac have their own shortcut settings so you can set them independently


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

Set default user:

Edit  `/etc/wsl.conf':

```
[user]
default=username
```

## Bitwarden

Use Bitwarden CLI from Ubuntu:

```sh
bw unlock
...  
export BW_SESSION="<somekey>"

bw list items | jq '.'
```

## Clonezilla

- Install [Clonezilla live](https://clonezilla.org/clonezilla-live.php) on USB
- Follow ["Disk to disk clone"](https://clonezilla.org/clonezilla-live-doc.php) but use 1st option (default settings)
- Copy "disk_to_local_disk"
- Use *Advanced Install* and select `-k1` option to copy to larger target disk

## Capsicain (not used!)

Low-level keyboard mapper.
See: https://github.com/cajhin/capsicain/issues/5
Edit c:\Users\paulc\capsican\capsican.ini
run c:\Users\paulc\capsican\capsican.exe

ESC-h - help
ESC-x - stop
ESC-1 - Apple Mac Alt-Win swap

https://github.com/cajhin/capsicain/wiki/Basics