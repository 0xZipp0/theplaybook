---
title: Dynamic Window Manager (Suckless)
date: 2017-04-02
tags:
  - "DWM"
  - "Suckless"
  - "Linux"
  - "Arch"
categories:
  - "Development"
  - "Suckless"
---

<aside>
💡 DWM Guidelines

</aside>

# **What is DWM ?**

The suckless developers created DWM, which stands for dynamic window manager. It’s a tiling window manager with a minimalist design. It takes less than 500MB of RAM and only a few processes when it first starts up. First and foremost, there is only one correct way to install dwm and other Suckless tools: from source (either straight from suckless.org , or from your own build, or from someone else’s build, though I wouldn’t recommend that unless you really trust the person) and compile it yourself.

# **Dependencies for DWM**

```jsx
**On Arch Linux:**sudo pacman -S base base-devel libx11 libxft libxinerama freetype2 fontconfig
{after installation & before install dwm}: sudo pacman -S xorg xorg-server xorg-apps xorg-xinit xclip
```

> ~~*<!> we have already install all dependencies of dwm before <!>*~~
> 

# **How to install DWM**

Here i have already installed Arch and gnome in it, hence i also have a Display manager and will be explaining , on how to start dwm using startx.

**step1: Clone**

As a result, the first step in installing DWM is to clone the github source from suckless or anyone else. Let’s now create a directory for all of the repositories(You may call your directory whatever you wish)

```
mkdir .suckless
cd .suckless
```

Now lets clone the repository required for our build , here for simplicity we will be using st as the default terminal. You can change your preferred terminal in config.h file in the dwm directory.

```
git clone https://github.com/0xzipp0/dwm.git
git clone https://github.com/0xZipp0/st.git
git clone https://github.com/0xzipp0/dmenu.git
```

**step 2 : Compiling the code**

Now to need to go the dwm , st and dmenu directory to `make` and then run `sudo make install`. For making it easier for you , you can copy the code below and run it in your terminal.

```
cd .suckless
cd dwm
make
sudo make clean install
cd..

cd st
make
sudo make clean install
cd ..

cd dmenu
make
sudo make clean install
cd
```

**step 3 :Starting dwm**

If you’re using startx, simply add exec /usr/local/bin/dwm at the end of your .xinitrc file on Linux

```jsx
**cp /etc/X11/xinit/xinitrc /home/user/.xinitrc** 
```

edit this copy of the pre exisitng .xinitrc file by delting last fe lines and replacing with e

exec dwm or with loop:

```bash
while true; do
				dwm >/dev/null 2&1
done
```