# User Interface Tweak

## GNOME
GNOME is lauched through the `gdm.service` systemd service. Enable and disable it by interracting with this service.

## LightDM
[arch documentation](https://wiki.archlinux.org/index.php/LightDM)

LightDM executes `*.desktop` files in `/usr/share/xsessions`. To execute `.xinitrc` file, copy `awesome.desktop` in `xinitrc.desktop` and make it exec `.xinitrc`.
