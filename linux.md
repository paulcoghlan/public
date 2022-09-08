# Linux

## Ubuntu

### Packages

```sh
List packages:
dkpg --list
apt list --installed

Package detail:
dpkg -s <package name>

Remove packages:
sudo apt-get --purge remove

```

### Services

List failed units

```sh
sudo systemctl list-units --failed
```

### Keyboard

Apple UK ISO Keyboard with Ubuntu: [https://2e0pgs.github.io/blog/sysadmin/2021/04/03/apple-keyboard-ubuntu/]

Add the following lines to  `/etc/modprobe.d/hid_apple.conf`:

```sh
options hid_apple fnmode=2
options hid_apple iso_layout=0
```

## Terminals

- Terminal is hardware device
- Terminal Emulator is software emulation of hardware
- Console is directly attached for maintenance etc
- Virtual Console is /dev/console
- Shell exposes OS services
- TTY (Teletype) is hardcopy terminal, before computer displays! tty is the software emulation

`stty` sets terminal options

- `stty -a` shows current settings
- `stty sane` resets settings
- `stty erase ^H` makes CTRL-H do same thing as backspace

`bind` (bash) or `bindkey` (zsh) shows keys bindings

- Check keymap using `set -o`
- Set VI keymap using `set -o vi`

## Systems Monitoring

vmstat - virtual memory stats
   Procs:
       r: The number of runnable processes (running or waiting for run time).
       b: The number of processes in uninterruptible sleep.

   Memory
       These are affected by the --unit option.
       swpd: the amount of virtual memory used.
       free: the amount of idle memory.
       buff: the amount of memory used as buffers.
       cache: the amount of memory used as cache.
       inact: the amount of inactive memory.  (-a option)
       active: the amount of active memory.  (-a option)

   Swap
       These are affected by the --unit option.
       si: Amount of memory swapped in from disk (/s).
       so: Amount of memory swapped to disk (/s).

   IO
       bi: Blocks received from a block device (blocks/s).
       bo: Blocks sent to a block device (blocks/s).

   System
       in: The number of interrupts per second, including the clock.
       cs: The number of context switches per second.

   CPU
       These are percentages of total CPU time.
       us: Time spent running non-kernel code.  (user time, including nice time)
       sy: Time spent running kernel code.  (system time)
       id: Time spent idle.  Prior to Linux 2.5.41, this includes IO-wait time.

## POSIX Compliance

IEEE families of standards.  
OSX is compliant.  
Most Unixes are compliant but haven't paid the fees for certification.
