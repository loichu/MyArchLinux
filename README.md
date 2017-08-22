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
The volume group will be the container inside the PV. A VG can be on multiple PV but in our case we have only one PV. So let's say that it's basically the same thing. Anyway we have to make one: `$ vgcreate ARCH /dev/sda2`. You'll be later able to access it with `/dev/ARCH/your-logical-volume` from the live session. It will be `/dev/mappers/ARCH-your-logical-volume` from your final arch install.
### Logical Volumes
We'll need at least two logical volumes. But we'll make three to have the home directory in a sepparated LV.
* Swap 8G (`/dev/ARCH/swap` and `/dev/mappers/ARCH-swap`).
* Root 50G (`/dev/ARCH-LVM/root` and `/dev/mappers/ARCH-root`).
* Home will take the remaining space (`/dev/ARCH-LVM/home` and `/dev/mappers/arch-home`).
The command to create a logical volume is: `$ lvcreate -L <size> <volume_group> -n <logical_volume>`.

For exemple: `$ lvcreate -L 8G ARCH -n swap`.

For the home directory it's a bit different because we want to take all the remaining space. It will be: `lvcreate -l 100%FREE ARCH -n home`. Look at the lowercase `-l` option ;).

## Formatting
See [official documentation](https://wiki.archlinux.org/index.php/File_systems#Create_a_file_system)

To create the file system, we'll use a tool called mkfs. We'll have different types of file systems.

| Partitions's name | File system | Command                      |
|-------------------|-------------|------------------------------|
| sda1              | fat32       | `$ mkfs.vfat /dev/sda1`      |
| ARCH-swap         | swap        | `$ mkswap /dev/ARCH/swap`    |
| ARCH-root         | ext4        | `$ mkfs.ext4 /dev/ARCH/root` |
| ARCH-home         | ext4        | `$ mkfs.ext4 /dev/ARCH/home` |

## Mounting
First we'll mount the root partition in `/mnt` using `mount` tool. Enter `$ mount /dev/ARCH/root /mnt`. Then we'll have to make two directories: one for the home partition and one for the ESP. So run `$ mkdir /mnt/home` and `$ mkdir /mnt/boot`. Then mount home and ESP in those directories. `$ mount /dev/ARCH/home /mnt/home` and `$ mount /dev/sda1 /mnt/boot`.

For the swap it's a bit different, we don't mount that partition. We swapon that partition: `$ swapon /dev/ARCH/swap`.

## Installation YEAH !
You can manage the mirrors in `/etc/pacman.d/mirrorlist` if you want. The upper they are in the file, the more they'll be chosen. You can also comment some of them if you don't want them at all. Personnally, I don't care, it's only the mirrors for the live iso session, not the final system. 

To install just run `$ pacstrap /mnt`. Then just wait a few moment.

## Configuration
### Fstab
See [official documentation](https://wiki.archlinux.org/index.php/Fstab)

The fstab is used to tell your system which partition to mount at startup. First let's generate it and see what appens: `$ genfstab -U /mnt >> /mnt/etc/fstab`. Our partitions will be identified by their UUID, it's less convenient to use but it avoids system's change issues. If you have mounted your partitions and the swap correctly, it should have generate a correct fstab.

Now make `$ cat /mnt/etc/fstab`. You sould see four partitions: root, home, swap and ESP. If it's not the case, open this file with vim, add a line and run `:r !lsblk -f`([append command output](http://vim.wikia.com/wiki/Append_output_of_an_external_command)). It will append the output of this command in the file. Then you'll be able to copy/paste the UUID of the missing partition ([select copy and paste](http://vim.wikia.com/wiki/Cut/copy_and_paste_using_visual_selection)).
### Change root
Now we want to move from the iso live session to our system. We'll use `$ arch-chroot /mnt`.
### Time zone
`$ ln -sf /usr/share/zoneinfo/Europe/Zurich /etc/localtime` then `$ hwclock --systohc`. Now you should have the correct time when you run `$ date`.
### Locales
First uncomment the desired locales in `/etc/locale.gen`. Then run `$ locale-gen`. Choose the one you want as primary language by setting the *LANG* parameter in `/etc/locale.conf`. For exemple: `LANG=en_US.UTF-8`.
### Hostname
Simply write the hostname in `/etc/hostname` and consider adding a matching entry in `/etc/hosts`.
### Systemd and LVM boot
We want to boot with systemd and LVM, which is not the case by default. We'll need to edit the file `/etc/mkinitcpio.conf` under `HOOKS=...`. We'll replace `udev` by `systemd` and add `sd-lvm2` between `block` and `filesystems`. Then run `$ mkinitcpio -p linux`.
### Set root password
Simply run `passwd`.
### Install boot loader
[Official documentation](https://wiki.archlinux.org/index.php/Systemd-boot)
Download the package *intel-ucode*: `$ pacman -S intel-ucode`. Run the command `$ bootctl install`. You'd got an error, don't worry we'll create this file mannually. Run `$ vim /boot/loader/loader.conf` and write:
```
default  arch-lvm
timeout  4
editor   1
```
Then run `$ vim /boot/loader/entries/arch-lvm.conf` and write:
```
title    Arch Linux (LVM)
linux    /vmlinuz-linux
initrd   /intel-ucode.img
initrd   /initramfs-linux.img
options  root=UUID=<your-root-partition-UUID>
```
## Reboot
**May the force be with you**
