# Make it usable !
## Make the shell great agin
Install zsh, grml-zsh-config and terminus-font. `$ pacman -S zsh zsh-completions grml-zsh-config terminus-font`. Make it the default shell `$ chsh /usr/bin/zsh`. Find the good font `$ setfont ter-v28n` where `28` is the size. Once you found the perfect font size, make it persistent by writing `FONT=<your-font>` in `/etc/vconsole.conf`.

## Create a user
[Doc for groups and users](https://wiki.archlinux.org/index.php/Users_and_groups)

[Doc for sudo managment](https://wiki.archlinux.org/index.php/Sudo)

First, install sudo to be able to create a sudo user `$ pacman -S sudo`. Then open the sudo config file `$ visudo` and uncomment the line `%wheel ALL=(ALL) ALL`. That will allow any user in the group wheel to run any command with sudo. Afterwards you can add a user and add him in the wheel group in one command `$ useradd -m -G wheel -s /bin/zsh username`. Then add a password `$ passwd username` and type two times the password. Then I think that you don't want only a username but also a real firstname and lastname, so type `$ chfn -f "Firstname Lastname" username` (you must run it with root privilege). Once your user acount is correctly set up, you make impossible to log in with root for [security](https://wiki.archlinux.org/index.php/Security) reasons: `$ sudo passwd -l root`.

## NetworkManager
NetworkManager is necessary to manage network in GUI. Install it with pacman `$ pacman -S networkmanager`. Then enable the systemd service with `$ systemctl enable NetworkManager`.

## Yaourt
Follow [this tutorial](https://www.ostechnix.com/install-yaourt-arch-linux/)

Add archlinuxfr source in `/etc/pacman.conf`:
```
[archlinuxfr]
SigLevel = Never
Server = http://repo.archlinux.fr/$arch
```
Install it with pacman: `$ pacman -Sy yaourt`.

## Graphical interface
> **This section will apply to laptops with intel HD integrated graphics only.**

Open `/etc/mkinitcpio.conf` in your editor and add `intel_agp i915` after `MODULES=`. Then rebuild your initial ramdisk image `$ mkinitcpio`. Now I suggest to download the driver `$ pacman -S mesa vulkan-intel`.

### Install a display manager
One could install Xorg, but I will install Wayland. Because Wayland is the future, Wayland is love, Wayland is life. So simply install it by running `$ pacman -S wayland`

### Install GUI
I choosed two different GUI that are supported by wayland: GNOME and sway. Sway is similar to i3 but under wayland. Install them by running `$ pacman -S sway gnome gnome-extra`.
#### GUI login
You have to enable the systemd service called gdm `$ systemctl enable gdm`.

## Debugging
### Error unknown signature
`$ pacman-key --refresh-keys`
