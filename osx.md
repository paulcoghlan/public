# OSX

## Mission Control

- `CMD+H` - hide active application window
- `CMD+ALT+H` - hide other application windows

Uncheck `Displays have separate Spaces` to allow a window to cover multiple desktops

## Screenshots

- `SHFT-CMD-4` - save application screenshot, then press spacebar
- `SHFT-CMD-3` - save whole screenshot

## Finder

Show the full directory path in the Finder window.

```sh
defaults write com.apple.finder AppleShowAllFiles -boolean true
killall Finder
```

- `SHFT-CMD-G` - to file name complete in Finder (use tab to complete)
- `SHFT-CMD-.` - show/hide hidden files in Finder

## Networking

Check VPN subnet: `netstat -rn | grep 192.168.44.`

List ports: `sudo lsof -i -n -P | grep TCP`

Dump Packets examples:

- `tcpdump -i en1 -A -n -s0 -vv tcp`
- `tcpdump -i lo0 -A -n -s0 -vv tcp`
- `tcpdump -i lo0 -A -n -s0 -vv src port 4566 or dst port 4566 > tcpdump.log`
- `tcpdump -i lo0 -A -n -s0  'tcp src port 4566 or tcp dst port 4566 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'` show all tcp data to/from port 4566

Open ports:

- `sudo lsof -PiTCP`
- `sudo lsof -PiUDP`
- `sudo lsof -Pn -i4`

Flush cache in Big Sur: `sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder`

## Admin

`open -a ApplicationName` - open application in /Applications

`CTRL+ALT+ESC` - kill process dialog

`ssh-add -l` - list keys in SSH Agent

`ssh-add -K <key>` - add key to SSH Agent

Stop/Start Services:

```sh
launchctl stop stunnel_startup
launchctl start stunnel_startup
```

Create Disk Image:

```sh
# List disk devices:
sudo diskutil list

# ** make sure you unmount from diskutil - ejecting will mean you can't dd
sudo diskutil unmountDisk /dev/diskN

# Copy disk to image file:
sudo dd if=<from diskutil> of=xxx  bs=16m
```

pbcopy/pbpaste copies to/from clipboard

```sh
echo "ohai im in ur clipboardz" | pbcopy
pbpaste > newfile.txt
```

## Terminal

`CMD-R` - End of Line

## Brew

List brew installations:
`brew list`

List top-level brew installation:
`brew leaves`

List package dependencies:
`brew deps --tree --installed <package>`

Uninstall brew package:
`brew uninstall xxx`

JEnv installation:

```sh
brew install jenv
jenv add /Library/Java/JavaVirtualMachines/jdk1.7.0_45.jdk/Contents/Home
jenv global oracle64-1.7.0.45
```

## TimeMachine

Setup TimeMachine using SMB:
`sudo tmutil setdestination smb://<user>:<password>@server._smb._tcp.local/TimeMachine`

Free Disc Space: Time Machine makes local backups in `/Volumes` - 100GB+ on my 500GB drive - disable from UI by unticking 'Backup Automatically', waiting for files to be deleted, then re-enabling.

## GMail

`e` - archive email

`Enter` - open email

`x` - select email

``,~` - change tab

`m` - mute conversation

## Slack

`CMD + /` - help

`ALT + UP/DOWN` - change channels

`CMD + SHIFT + \` - respond with emoticon

`CMD + K` - quick switcher

`SHIFT + ESC` - mark all as read

`/dnd` - Sleep!

## ClamAV

Update database:
freshclam

Use `clam <path>` alias to scan:
`clam $HOME`

Look in `$HOME/infected` and `$HOME/clamscan.log` for results

## Chrome

`ALT+` - change window

DNS: `chrome://net-internals/#dns`

`CMD+SHIFT+[/]` - left right tabs

`CMD+[` - back in history

`CMD+L` - enter URL

`CMD+W` - close window or current tab

`ALT+CMD+U` - show source

`ALT+CMD+I` - open dev tools

`F1` - show keys in dev tools

`ESC` - show/hide console

`CTRL+L` - clear console

`CMD+F` - search in dev tools

`CMD+` ` - switch from browser window to tools window

`CMD+[ or ]` - switch tabs in dev tools

`ALT+CMD+ [ or ]` - forwards/back in dev tools

`F8` - Pause/Continue debugger

`F10` - Step Over

`F11` - Step Into

## OBS - Virtual Audio

1. Install [https://github.com/ExistentialAudio/BlackHole]:
`brew install blackhole-2ch`

2. Enable monitoring from OBS to blackhole: `Preferences > Audio > Advanced`
Select Blackhole 2ch

3. Click on Audio Input Capture Cog
   3.1 Select Advanced Audio Properties
   3.2 Under Audio Monitoring (scroll to right if necessary) select Monitor Only
   3.3 In Google Meeting settings, select Blackhole
   3.4 Adjust Sync Offset in Advanced Audio Properties

## Github

CMD+K - Command Palette

## FZF

```sh
brew install fzf
make up | fzf --tac --height 100% --no-sort --ansi
```
