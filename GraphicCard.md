# Graphic card

## Links
* [NVIDIA driver](https://www.nvidia.com/Download/driverResults.aspx/137276/en-us)
* [bumblebee archlinux doc](https://wiki.archlinux.org/index.php/Bumblebee)
* [NVIDIA archlinux doc](https://wiki.archlinux.org/index.php/Bumblebee)

## To Do
* Download packages bumblebee, nvidia-390xx, mesa, xf86-video-intel
* Add your user to bumblebee group `gpasswd -a $(whoami) bumblebee`
* Enable service `bumblebeed.service`
* Create a pacman hook to update initramfs when nvidia driver is updated

## Pacman hook
`/etc/pacman.d/hooks/nvidia.hook`
```
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is used

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```

## Use bbswitch
Hers's the configuration file:

`/etc/bumblebee/bumblebee.conf`
```
[bumblebeed]
VirtualDisplay=:8
KeepUnusedXServer=false
ServerGroup=bumblebee
TurnCardOffAtExit=false
NoEcoModeOverride=false
Driver=nvidia
XorgConfDir=/etc/bumblebee/xorg.conf.d

[optirun]
Bridge=auto
VGLTransport=proxy
PrimusLibraryPath=/usr/lib/primus:/usr/lib32/primus
AllowFallbackToIGC=false

[driver-nvidia]
KernelDriver=nvidia
PMMethod=bbswitch
LibraryPath=/usr/lib/nvidia:/usr/lib32/nvidia:/usr/lib:/usr/lib32
XorgModulePath=/usr/lib/nvidia/xorg,/usr/lib/xorg/modules
XorgConfFile=/etc/bumblebee/xorg.conf.nvidia

[driver-nouveau]
KernelDriver=nouveau
PMMethod=auto
XorgConfFile=/etc/bumblebee/xorg.conf.nouveau
```
