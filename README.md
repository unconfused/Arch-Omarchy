# Arch-Omarchy

This is my current setup for a minimalist feeling, but very functional Linux setup. Also, note that this is primarily a single user setup.

### Omarchy = an opinionated Arch + Hyprland setup
Watch the videos...and get some background as to why this is a good choice compared to just an Arch install, or picking a bigger install based on Arch like CachyOS.<br>
[https://omarchy.org/](https://omarchy.org/)


### OS / Windows Manager / System Setup

Download Arch Linux:<br>
[https://archlinux.org/](https://archlinux.org/)


Configure your install with these instructions:<br>
[https://manuals.omamix.org/2/the-omarchy-manual/50/getting-started](https://manuals.omamix.org/2/the-omarchy-manual/50/getting-started)

Pay special attention on a few of the parts...as they aren't very clear. Also, you might think you don't want the disk encryption, but it is important to the setup (and really disk encryption is important for you and your data). The Omarchy setup will essentially use disk encryption pass as your only login.  As with anything, there is always a way to enable shell logins again, and not use encryption.

Under "Partitioning":
1. I picked Manual partitioning.
2. Put an X on the physical disk you want to configure.
3. Delete partitions as necessary, and then create the partitions you want or use "suggest partition layout" (which I used the latter).
4. Pick the "btrfs" filesystem.
5. Pick "Use BTRFS subvolumes with default structure".
6. Pick "Use compression".
7. Review the layout...and if okay pick "Confirm and exit"

Under "Disk Encryption":
1. Encryption type: LUKS
2. Encryption password: pick the password you probably also want for your user you create later in the Arch install config.
3. Partitions: pick the BTRFS volume you partitioned in the prior step.

Under "Applications" in addition to using "pipewire" for audio, I enable bluetooth. 

Outside of those, I'm still pretty much just following the directions.

### Install Omarchy

Once Arch is done installing and reboots, relogin...and then I used the minimal install for Omarchy too:
```
wget -qO- https://omarchy.org/install-bare | bash
```
When it gets to [1/5] and asks for your full name, it is asking for GitHub. If you have a GitHub account, give it your GitHub username. Then for email, give it your GitHub email.

If something fails or the process gets interrupted...or you just want to examine any of the scripts...the install that was initiated are in:
```
~/.local/share/omarchy/
```
Thus the install itself is the script:
```
~/.local/share/omarchy/install.sh
```

Once it is done installing it will automatically reboot.

You can use the mouse and click the circle in the upper-left of the screen. Or you can use hotkeys. The button with the Windows logo is the "Super" key. Your basic important hotkeys are:
1. Super-Space to bring up the apps menu.
2. Super-Alt-Space to bring up the system menu.
3. Super-1, Super-2, Super-3, etc. to switch to a specific desktop.
4. Super-W to close an application.
5. Super-\[mouse drag\] to move an application to a new postion.
6. If you have a app on, say, desktop 3....and you want it to be on desktop 2...hit Super-Shift-2 and it will jump to that desktop.
7. Anytime you are lost and want to see the hotkeys...hit Super-K


### Beginnings

First we need to test if the resolution and scaling you see on the screen is working for you and your hardware. Open an couple apps...like a browser or a terminal.  If the scaling seems like things are too big on the screen, it is because the auto scaling isn't adjusting for your monitor quite right. But it is easily changed.

Let us prep the Neovim editor, and then edit the files. Open an Alacritty terminal. Then type 'nvim' to start Neovim.  It will download all it's plugins and configure itself. Once it is done...hit 'q' to close the update window....and then 'q' again to close Neovim.

Type ```nvim ~/.config/hypr/monitors.conf```.  Currently it has the following in it:
```
env = GDK_SCALE,2
monitor=,preferred,auto,auto
```

For a 1080p screen @ 60Hz, which scaling at 1:1, make some changes. So, to actually insert/edit the text, press 'i'. Change those lines to:
```
env = GDK_SCALE,1
monitor=,preferred,1920x1024@60,1
```

To save it, hit escape to get out of insert mode, press ':' to give nvim a command, type 'wq' for write and quit, hit enter.  If you need to quit and discard changes, hit ':' and then 'q!', enter.

If something still is off, you can reedit that and play with the settings. There are other examples in the file.


### Install Applications

There are two ways to install apps...the easiest way is hit Super-Alt-Space, go to Install, go to Packages, let it refresh the list, and then type in the application package name you want.  The second way is to open up an Alacritty terminal and use: ```sudo pacman -S <packagename>```.  This second way you can add as many packages as you want in one command.

List of apps I add:
1. ghostty - I think this is better than Alacritty
2. firefox - Chromium is already installed...but I like firefox.
3. libreoffice-fresh - everyone needs an office suite
4. gimp - full-featured image editor
5. enpass-bin - This enpass is in the AUR, so you have to use ```sudo yay -S enpass-bin``` to install it.
6. python3 - Super-Alt-Space, Install, Development, Python
7. rust - Super-Alt-Space, Install, Development, Rust
8. Visual Studio Code - Super-Alt-Space, Install, Editor, VSCode
9. hdparm - controls for HDDs


### Post-Install Items

On my old laptop there is a spinning-disc HDD.  I had an issue with it running constantly and never suspending.  I had to do the following:
(/dev/sda is my HDD, /dev/nvme0n1 is my nvme)
Allow suspending...Set suspending the HDD to 5 minutes.  The number in the hdparm command is a calculation of 5-second intervals. [minutes]*60/5.  So, 5-minutes is 5*60/5=60. 10-minutes would be 10*60/5=120.
```
# this part is just temporary though...
sudo hdparm --yes-i-know-what-i-am-doing -s 1 /dev/sda
sudo hdparm -S 60 /dev/sda

# this is the permanent fix...
sudo udevadm info -q all -n /dev/sda | grep ID_SERIAL
sudo nvim /etc/udev/rules.d/99-spindown.rules

# Add the following line in your new .rules file...change my serial number for yours.
ACTION=="add|change", SUBSYSTEM=="block", ENV{ID_SERIAL}=="WDC_WD10JPVX-22JC3T0_WD-WX61C54F1234", RUN+="/usr/bin/hdparm -S 36 /dev/%k"

sudo udevadm control --reload-rules
sudo udevadm trigger

sudo hdparm -C /dev/sda
```

Enable the uncomplicated firewall:
```
sudo systemctl enable --now ufw.service
```

Add Oh-My-Posh to pretty up the terminal prompt:<br>
[https://ohmyposh.dev/docs/installation/linux](https://ohmyposh.dev/docs/installation/linux)
Install oh-my-posh...
```
curl -s https://ohmyposh.dev/install.sh | bash -s
```
Edit the .bashrc file 
```
nvim .bashrc
```
Hit 'i' to insert. Add this line at the very end of the file...I'm using the 'catppuccin' theme.
```
eval "$(oh-my-posh init bash --config /home/evan/.cache/oh-my-posh/themes/catppuccin.omp.json)"
```
Hit escape, ':' for a command, then 'wq' to write/quit. Then refresh bash...
```
source .bashrc
```


### Other stuff

Set up for .dotfiles usage...
```
cd ~
ssh-keygen -t ecdsa -b 521
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_ecdsa
cat ~/.ssh/id_ecdsa.pub
# add this to your Github > profile > setttings > SSH and GPG keys > new SSH key

mkdir ~/.dotfiles
cd ~/.dotfiles
git init
git remote add origin git@github.com:unconfused/dotfiles.git
git pull origin master
```


