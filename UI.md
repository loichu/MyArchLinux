# User Interface Tweak

## GNOME
GNOME is lauched through the `gdm.service` systemd service. Enable and disable it by interracting with this service.

## LightDM
[arch documentation](https://wiki.archlinux.org/index.php/LightDM)

LightDM executes `*.desktop` files in `/usr/share/xsessions`.

LightDM uses `systemd` to launch programs. Therefore, `~/.xinitrc` is not executed. Put your user config into `~/.xprofile` and the global one (i.e. keyboard layouts) in `/etc/xprofile`. Everything in `/etc/xprofile` will be available in LightDM but keep in mind that no user is logged so if you need so `$HOME` is not set. Also the WM is launched through `/usr/share/xsessions/*.desktop` so don't launch it in `xprofile`.

`~/.xinitrc` is still useful if you launch `startx` from a raw tty. Source your `xprofile`s in this file to avoid configuration repetition.
