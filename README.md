# MyArchLinux
This repo is made to have a custom installation process and configuration for ArchLinux. 

## Configuration
* Laptop HP spectre x360 2016
* UEFI boot mode
* LVM partitionning
* Home directory in separated partition
* Grub2 bootloader
* Zsh default shell
* Yaourt and Pacman package managers
* Sway and I3 WM

## Prepare Arch USB and run it
The best way I found to make a UEFI usb stick is to use Rufus. Unfortunately it's only available on Windows. Dowload the latest arch iso on the [official website](https://www.archlinux.org/download/). And burn it to a usb stick using [Rufus](https://rufus.akeo.ie/). Make sure it's in UEFI - GPT mode.

Boot pressing F9 and select the usb stick. Normally the Arch loader should appear, select first entry.

## Basic configuration
### Keyboard
Default keyboard layout is US. You can change to swiss keyboard with `$ loadkeys fr_CH-latin1` command. Revert with `$ loadkeys us`.
### Network
If you have no ethernet plug in your laptop don't worry ! You can either plug a usb adapter or use your phone USB connexion sharing. Make a `$ ip a` to see if you have an IP. If you don't make sure your interface is up. `$ ip link set interface up
` to enable it. Replace `interface` by your interface's name, you see it with `$ ip a`. Then run `$ dhcpcd` to find a available IP address and automatically assign it to your interface.
