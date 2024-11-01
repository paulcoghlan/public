# NixOS from scratch

Using https://nixos.org/manual/nixos/stable/#sec-booting-from-usb:
1. Create USB stick with non-UI installer
2. Boot from USB
3. Add password to nixos user with `passwd` command
4. ssh 
5. Create partition using UEFI boot:
```
# Change to root user
sudo su -

parted /dev/nvme0n1 -- mklabel gpt
parted /dev/nvme0n1  -- mkpart root ext4 512MB -32GB
parted /dev/nvme0n1  -- mkpart swap linux-swap -32GB 100% 
parted /dev/nvme0n1 -- mkpart ESP fat32 1MB 512MB
parted /dev/nvme0n1 -- set 3 esp on
parted /dev/nvme0n1  print
Model: Samsung SSD 970 EVO 1TB (nvme)
Disk /dev/nvme0n1: 1000GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name  Flags
 3      1049kB  512MB   511MB   fat32        ESP   boot, esp
 1      512MB   968GB   968GB                root
 2      968GB   1000GB  32.0GB               swap  swap

# Formatting
mkfs.ext4 -L nixos /dev/nvme0n1p1
mkswap -L swap /dev/nvme0n1p2
mkfs.fat -F 32 -n boot /dev/nvme0n1p3

# Install
mount /dev/disk/by-label/nixos /mnt
mkdir -p /mnt/boot
mount -o umask=077 /dev/disk/by-label/boot /mnt/boot
swapon /dev/nvme0n1p2

nixos-generate-config --root /mnt

# Edit configuration.nix to add:
boot.loader.systemd-boot.enable = true;
networking.hostName = "homelab"; # Define your hostname.
services.openssh.enable = true;

# Install to local FS
nixos-install

# Reboot
reboot

# Login as root
passd paul

# You can now ssh as paul@

# Add users.users."user".openssh.authorizedKeys.keys

# Rebuild and switch to new build
nixos-rebuild switch
```

Build and nothing more
`nixos-rebuild build`

```
  # ZFS Support
  boot.supportedFilesystems = [ "zfs" ];
  boot.zfs.forceImportRoot = false;
  # Generate hostId from:
  #  head -c4 /dev/urandom | od -A none -t x4
  networking.hostId = "y2809e7c"

  boot = {
  initrd.network = {
    postCommands = ''
	wget -O /etc/zfs/enc3key  http://192.168.2.97:8123/local/enc3key
	zpool import home2
	zfs mount -a -l
    '';
  };
};
  
  ```
  ``

## Samba Password Config

Doesn't fit declarative model! See https://discourse.nixos.org/t/nixos-configuration-for-samba/17079/3:
```
system.activationScripts = { sambaUserSetup = { text = '' PATH=$PATH:${lib.makeBinPath [ pkgs.samba ]} pdbedit -i smbpasswd:/home/john/smbpasswd -e tdbsam:/var/lib/samba/private/passdb.tdb ''; deps = [ ]; }; };
```

```
smbpasswd -a paul
smbpasswd -a kate
smbpasswd -a appletv

# Check with:
pdbedit -L -v
smbclient --list localhost -U paul
smbclient -U appletv //localhost/paul

```

https://nixos.org/manual/nixos/stable/#sec-installation-manual-installing

TODO: 
See router-build and openmediavault in private repo: 
check samba config for timemachine
install backup sync - Sanoid, Syncoid
traefik install

## SystemD Tricks

Get all boots: `journalctl --list-boots`
Get most recent boot logs: `journalctl -b 0`
Service logs: `journalctl -u samba-smbd.service -f`

## Install Nix

The recommended multi-user Nix installation creates system users, and a system service for the Nix daemon.

1. Multi-user install: `sh <(curl -L https://nixos.org/nix/install)`
2. Add `direnv` as a convenience: `nix-env -i direnv`

## Workflow

1. Edit `flake.nix`
2. Change to directory containing `flake.nix` ???


## Homelab

Some user blogs:
[https://www.heinrichhartmann.com/posts/home-lab-2023/](https://www.heinrichhartmann.com/posts/home-lab-2023/)  
[https://dev.to/jasper-at-windswept/the-ultimate-nixos-homelab-guide-the-install-53k7](https://dev.to/jasper-at-windswept/the-ultimate-nixos-homelab-guide-the-install-53k7)  

Offical docs:
[https://nixos.wiki/wiki/ZFS](https://nixos.wiki/wiki/ZFS)  
[https://openzfs.github.io/openzfs-docs/Getting%20Started/NixOS/](https://openzfs.github.io/openzfs-docs/Getting%20Started/NixOS/)  
[https://nixos.wiki/wiki/Samba](https://nixos.wiki/wiki/Samba)  

From Tailscale'\s alex-kretzschmar:
[https://perfectmediaserver.com/02-tech-stack/nixos/](https://perfectmediaserver.com/02-tech-stack/nixos/)

## Useful Resources

Explain Nix syntax visually: https://zaynetro.com/explainix