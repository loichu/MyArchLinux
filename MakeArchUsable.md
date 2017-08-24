# Make it usable !
## Make the shell great agin
Install zsh, grml-zsh-config and terminus-font. `$ pacman -S zsh zsh-completions grml-zsh-config terminus-font`. Make it the default shell `$ chsh /usr/bin/zsh`. Find the good font `$ setfont ter-v28n` where `28` is the size. Once you found the perfect font size, make it persistent by writing `FONT=<your-font>` in `/etc/vconsole.conf`.
## Create a user
[Doc for groups and users](https://wiki.archlinux.org/index.php/Users_and_groups)

[Doc for sudo managment](https://wiki.archlinux.org/index.php/Sudo)

First, install sudo to be able to create a sudo user `$ pacman -S sudo`. Then open the sudo config file `$ visudo` and uncomment the line `%wheel ALL=(ALL) ALL`. That will allow any user in the group wheel to run any command with sudo. Afterwards you can add a user and add him in the wheel group in one command `$ useradd -m -G wheel -s /bin/zsh username`. Then add a password `$ passwd username` and type two times the password. Then I think that you don't want only a username but also a real firstname and lastname, so type `$ chfn -f "Firstname Lastname" username` (you must run it with root privilege). Once your user acount is correctly set up, you make impossible to log in with root for [security](https://wiki.archlinux.org/index.php/Security) reasons: `$ sudo passwd -l root`.
