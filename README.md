# MyArchLinux
This repo is made to have a custom installation process and configuration for ArchLinux. 

Inspired from this [guide](https://wiki.archlinux.org/index.php/installation_guide)

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
See [official documentation](https://wiki.archlinux.org/index.php/Network_configuration)

If you have no ethernet plug in your laptop don't worry ! You can either plug a usb adapter or use your phone USB connexion sharing. Make a `$ ip a` to see if you have an IP. If you don't make sure your interface is up. `$ ip link set interface up
` to enable it. Replace `interface` by your interface's name, you see it with `$ ip a`. Then run `$ dhcpcd` to find a available IP address and automatically assign it to your interface.

## Partitioning
See [official documentation](https://wiki.archlinux.org/index.php/Partitioning)
### Partition Table
I suggest to use fdisk to partition the disk. `$ gdisk /dev/sda` to go in interractive partitioning. Press `o` to create a new GPT partition table. 
### EFI System Partition
See [official documentation]https://wiki.archlinux.org/index.php/EFI_System_Partition)

Press `n` to create a new partition. Accept the defaults for the partition number and first sector. For the last sector enter `+512M`. When it asks to choose a partition type, enter `EF00` that means: EFI System Partition.
### LVM Partition
Press `n` again to create a new partition. Accept all defaults but the partition type. At this moment enter `8e00` that means: Linux LVM.

Press `w` to write changes.
## LVM
See [official documentation](https://wiki.archlinux.org/index.php/LVM)
### Physical Volume
As we have created an EFI System Partition on /dev/sda1, we'll create our physical on /dev/sda2. We just have to enter the command `$ pvcreate /dev/sda2`. `$ pvdisplay` to see the freshly created PV.
### Volume Group
The volume group will be the container inside the PV. A VG can be on multiple PV but in our case we have only one PV. So let's say that it's basically the same thing. Anyway we have to make one: `vgcreate ARCH-LVM /dev/sda2`. You'll be later able to access it with `/dev/ARCH-LVM/your-logical-volume`
### Logical Volumes
We'll need at least two logical volumes. But we'll make three to have the home directory in a sepparated LV.
* Swap 8G (`/dev/ARCH-LVM/ARCH-swap`)
* Root 50G (`/dev/ARCH-LVM/ARCH-root`)
* Home will take the remaining space (`/dev/ARCH-LVM/ARCH-home`)
The command to create a logical volume is: `lvcreate -L <size> <volume_group> -n <logical_volume>`.

For exemple: `lvcreate -L 8G ARCH-LVM -n ARCH-swap`.

For the home directory it's a bit different because we want to take all the remaining space. It will be: `lvcreate -l 100%FREE ARCH-LVM -n ARCH-home`. Look at the lowercase `-l` option ;).

## Formatting
See [official documentation](https://wiki.archlinux.org/index.php/File_systems#Create_a_file_system)

To create the file system, we'll use a tool called mkfs. We'll have different types of file systems.

| Partitions's name | File system | Command                             |
|-------------------|-------------|-------------------------------------|
| sda1              | fat32       | `mkfs.vfat /dev/sda1`               |
| ARCH-swap         | swap        | `mkswap /dev/ARCH-LVM/ARCH-swap`    |
| ARCH-root         | ext4        | `mkfs.ext4 /dev/ARCH-LVM/ARCH-root` |
| ARCH-home         | ext4        | `mkfs.ext4 /dev/ARCH-LVM/ARCH-home` |
