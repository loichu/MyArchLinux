# Make it usable !
## Make the shell great agin
Install zsh, grml-zsh-config and terminus-font. `$ pacman -S zsh grml-zsh-config terminus-font`. Make it the default shell `$ chsh /usr/bin/zsh`. Find the good font `$ setfont ter-v28n` where `28` is the size. Once you found the perfect font size, make it persistent by writing `FONT=<your-font>` in `/etc/vconsole.conf`.
## Create a user
First, install sudo to be able to create a sudo user `$ pacman -S sudo`.
