---
title: Arch Linux + DWM SUckless
description: dwm is a dynamic window manager for X.
date: 2018-04-16
categories:
  - "Development"
tags:
  - "Linux"
  - "Arch"
menu:
  main:
    name: Notes
    weight: 4
---

<aside>
💡 0x Note**: It’s based on experience and device environment, in another case maybe you deal with some issues or problems. you can visit the arch forum [here](https://bbs.archlinux.org/), or [here](https://www.linuxquestions.org/questions/arch-29/).**

</aside>
<!--more-->


<aside>
💡 0x Tip: Important to know your own Laptop, what kind of boot (efi or uefi) ssd or hdd, vm, single boot, or dual boot, etc.

</aside>

## [Pre-install steps](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#pre-install-steps)

### [Download and verify Arch Linux media](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#download-and-verify-arch-linux-media)

Download the current version of Arch Linux to a location on your filesystem. See the [Acquire an installation image](https://wiki.archlinux.org/index.php/Installation_guide#Acquire_an_installation_image) and [Verify Signature](https://wiki.archlinux.org/index.php/Installation_guide#Verify_signature) sections of the Arch Linux installation guide for instructions on how to retrieve and verify the current version of Arch Linux.s

I used [archlinux-2020.08.01-x86_64.iso](http://mirror.arizona.edu/archlinux/iso/2020.08.01/) with kernel `5.7.12-arch1-1` for this gist.

### [Create Virtual Machine](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#create-virtual-machine)

I'm going to trust that you have a good understanding of your hypervisor of choice for this bit =)

There are a handful of VM settings that really matter for this gist:

- Guest operating system:
    - **VMware Workstation:** Other Linux 5.x or later kernel 64-bit (`other5xlinux-64`)
    - **Oracle VirtualBox:** Arch Linux x64
- Optical drive for mounting install media
- 20GB virtual disk (commands in this gist are geared toward this size)
- SCSI or SATA disk controller (VMware Paravirtual and LSI Logic were tested, as well as VirtualBox SATA)
- UEFI boot mode:
    - **VMware Workstation:** VM > Settings > Options > Advanced > UEFI
    - **Oracle VirtualBox:** VM > Settings > System > Motherboard > Enable EFI (special OSes only)
- Network adapter connected to a network that provides [DHCP](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) (NAT, or usually Bridged)

Aside from the above, configure the virtual machine to your liking.

### [Boot system to Arch Linux live distro](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#boot-system-to-arch-linux-live-distro)

Mount the ISO image in the optical drive, start the virtual machine, and boot into the live Arch Linux installation distro.

## [Install steps](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#install-steps)

### [Check system capabilities](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#check-system-capabilities)

### [Verify boot mode](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#verify-boot-mode)

List contents of the efivars directory to verify boot mode is UEFI:

```
ls /sys/firmware/efi/efivars
```

If you get an error indicating that the directory does not exist, the system did not boot using UEFI, and you need to enable UEFI boot in VM settings

### [Verify network availability](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#verify-network-availability)

Use the following commands to check interface status and IP addressing:

```
ip link
ip addr
```

Example output:

```
root@archiso ~ # ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:98:d8:51 brd ff:ff:ff:ff:ff:ff
    altname enp2s1

root@archiso ~ # ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:98:d8:51 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.5.141/24 brd 192.168.5.255 scope global dynamic ens33
       valid_lft 1630sec preferred_lft 1630sec
    inet6 fe80::20c:29ff:fe98:d851/64 scope link
       valid_lft forever preferred_lft forever
```

Your interface name will vary depending on *factors*, but `lo` is **not** the one you're looking for - if that's the only interface you see, you'll need to troubleshoot. Some common primary interface names are `eth0`, `ens33`, `ens160`, and `enp0s3`. The default in VMware Workstation 15 is `ens33`.

### [Internet connection](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#internet-connection)

To make sure you have an internet connection, you have to ask Mr. Google:

```
   # ping 8.8.8.8
```

### [Add best Arch mirrors](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#add-best-arch-mirrors)

To install arch you have to download packages. It's a good idea to download them from the best connection mirror.

```
   # pacman -Sy
   # pacman -S reflector
   # reflector --latest 5 --sort rate --save /etc/pacman.d/mirrorlist
```

### [Sync network time](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#sync-network-time)

Ensure the system clock is synced (this is important for encrypted connections to succeed):

```
timedatectl set-ntp true
```

### [Configure storage](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#configure-storage)

### [Write partition table](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#write-partition-table)

### [Partition disk](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#partition-disk)

Your primary disk will be known from now on as `sda`. You can check if this is really your primary disk:

```
   # lsblk
```

Feel free to adapt the rest of the guide to `sdb` or any other if you want to install Arch on a secondary hard drive.

This guide will use a 250GB hard disk and will have only Arch Linux installed. You'll create 5 partitions of the disk (feel free to suit this to your needs).

- `/dev/sda1` boot partition (1G).
- `/dev/sda2` swap partition (4G).
- `/dev/sda3` root partition (50G).
- `/dev/sda4` home partition (100G).
- `/dev/sda5` data partition (remaining disk space).

You're going to start by removing all the previous partitions and creating the new ones.

```
   # gdisk /dev/sda
```

This interactive CLI program allows you to enter commands for managing your HD. I'm going to show you only the commands you need to enter.

### [Clear partitions table](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#clear-partitions-table)

```
   Command: O
   Y
```

### [EFI partition (boot)](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#efi-partition-boot)

```
   Command: N
   ENTER
   ENTER
   +1G
   EF00
```

### [SWAP partition](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#swap-partition)

```
   Command: N
   ENTER
   ENTER
   +4G
   8200
```

### [Root partition (/)](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#root-partition-)

```
   Command: N
   ENTER
   ENTER
   +50G
   8304
```

### [Home partition](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#home-partition)

```
   Command: N
   ENTER
   ENTER
   +100G
   8302
```

### [Data partition](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#data-partition)

```
   Command: N
   ENTER
   ENTER
   ENTER
   ENTER
```

### [Save changes and exit](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#save-changes-and-exit)

```
   Command: W
   Y
```

### [Format partitions](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#format-partitions)

```
   # mkfs.fat -F32 /dev/sda1
   # mkswap /dev/sda2
   # mkfs.ext4 /dev/sda3
   # mkfs.ext4 /dev/sda4
   # mkfs.ext4 /dev/sda5
```

### [Mount partitions](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#mount-partitions)

```
   # swapon /dev/sda2
   # mount /dev/sda3 /mnt
   # mkdir /mnt/{boot,home}
   # mount /dev/sda1 /mnt/boot
   # mount /dev/sda4 /mnt/home
```

If you run the `lsblk` command you should see something like this:

```
   NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
   sda      8:0    0 232.9G  0 disk
   ├─sda1   8:1    0     1G  0 part /mnt/boot
   ├─sda2   8:2    0     4G  0 part [SWAP]
   ├─sda3   8:3    0    50G  0 part /mnt
   ├─sda4   8:4    0   100G  0 part /mnt/home
   └─sda5   8:5    0  77.9G  0 part
```

### [Install packages and chroot into system](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#install-packages-and-chroot-into-system)

### [Install essential packages](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#install-essential-packages)

Run the following commands to install essential packages into the mounted partitions:

```
pacstrap /mnt linux linux-firmware grub efibootmgr vim base base-devel openssh
```

Don't worry about any localization related errors you may receive - these will be resolved by future steps.

While the [installation guide](https://wiki.archlinux.org/index.php/Installation_guide) only includes the `base`, `linux` and `linux-firmware` packages in this command, these packages are the minimum required to progress with installation. The extra packages specified in this guide are:

- `grub`: The boot loader we'll be using
- `efibootmgr`: A utility used to modify the EFI boot manager
- `lvm2`: Utilities for logical volume management (LVM)
- `vim`: A text editor

These are the same packages that would install using `pacman -S packagename`. You may want to install additional packages - go for it!

### [Update system's filesystem table](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#update-systems-filesystem-table)

Use the following command to generate fstab format mount entries and append those entries to the new system's `/etc/fstab` file:

```
genfstab -U /mnt >> /mnt/etc/fstab
```

## [Add basic configuration](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#add-basic-configuration)

### [Enter the new system](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#enter-the-new-system)

```
   # arch-chroot /mnt
```

### [Language-related settings](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#language-related-settings)

```
   # vim /etc/locale.gen
```

Now you have to uncomment the language of your choice, for example `en_US.UTF-8 UTF-8`.

```
   # locale-gen
   # vim /etc/locale.conf
```

Add this content to the file:

```
   LANG=en_US.UTF-8
   LANGUAGE=en_US
   LC_ALL=Cd
```

```
   # nvim /etc/vconsole.conf
```

Add this content to the file:

```
   KEYMAP=us
```

### [Configure timezone](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#configure-timezone)

For this example I'll use "Europe/Madrid", but adapt it to your zone.

```
   # ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime
   # hwclock —-systohc
```

### [Enable SSH, NetworkManager and DHCP](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#enable-ssh-networkmanager-and-dhcp)

These services will be started automatically when the system boots up.

```
   # pacman -S dhcpcd networkmanager network-manager-applet
   # systemctl enable sshd
   # systemctl enable dhcpcd
   # systemctl enable NetworkManager
```

### [Set root password](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#set-root-password)

Change the `root` user's password so login is possible on first boot into the new system:

```
passwd
```

### [Configure networking](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#configure-networking)

Run `ip link` to identify your interface name (`ens33` in this case):

```
[root@archiso /]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:98:d8:51 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
```

Edit `/etc/systemd/network/20-wired.network` to configure DHCP for your interface:

```
[Match]
Name=ens33 enp0s1n- ens160 

[Network]
DHCP=yes
```

Enable `systemd-networkd` and `sytemd-resolved` systemd services to start at boot:sas

```
systemctl enable systemd-networkd systemd-resolved
```

If you would prefer to set static addressing, or have multiple network interfaces, I recommend reviewing the [systemd-networkd](https://wiki.archlinux.org/index.php/Systemd-networkd) documentation in the [Arch Wiki](https://wiki.archlinux.org/).

### [Set hostname and hosts aliases](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#set-hostname-and-hosts-aliases)

Edit `/etc/hostname` and save the following line (or your preferred hostname) to it:

```
arch.localdomain
```

Edit `/etc/hosts` and save the following lines to it (modifying hostname as needed):

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch.localdomain arch
```

### [Generate boot image and configure boot loader (GRUB)](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#generate-boot-image-and-configure-boot-loader-grub)

### [Update initial ramdisk](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#update-initial-ramdisk)

In order for the `/etc/mkinitcpio.conf` change to apply on system boot, we need to update our initial ramdisk (initramfs).

Run the following command to generate a new initramfs image:

```
mkinitcpio -P
```

### [Install GRUB](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#install-grub)

-Run the following command to install the GRUB with an EFI target system/directory:

```
p# grub-install --target=x86_64-efi --efi-directory=/efi

</> or </>
grub-install --efi-directory=/boot --bootloader-id=GRUB
```

### [Configure GRUB](https://gist.github.com/miliarch/dd19af34679417b18f1bcdb680728a45#configure-grub)

Generate GRUB configurations under the `/boot` and `/efi` partitions:

```
grub-mkconfig -o /boot/grub/grub.cfg
#grub-mkconfig -o /efi/EFI/arch/grub.cfg
```

### [Install other useful packages](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#install-other-useful-packages)

```
   # pacman -S iw wpa_supplicant dialog intel-ucode git reflector lshw unzip
   # pacman -S wget pulseaudio alsa-utils alsa-plugins pavucontrol xdg-user-dirs
	 # pacman -S libx11 libxft libxinerama freetype2 fontconfig ttf-jetbrains-mono ttf-joypixels
```

### [Final steps](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#final-steps)

```
   # exit
   # umount -R /mnt
   # swapoff /dev/sda2
   # reboot
```

## [Post-install configuration](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#post-install-configuration)

Now your computer has restarted and in the login window on the tty1 console you can log in with the root user and the password chosen in the previous step.

### [Add your user](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#add-your-user)

Assuming your chosen user is "0x":

```
   # useradd -m -g users -G wheel,storage,power,audio 0x
   # passwd 0x
```

### [Grant root access to our user](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#grant-root-access-to-our-user)

```
   # EDITOR=vim visudo
```

If you prefer not to be prompted for a password every time you run a command with "sudo" privileges you need to uncomment this linet

```
   %wheel ALL=(ALL) NOPASSWD: ALL
```

Or if you prefer the standard behavior of most Linux distros you need to uncomment this line:

```
   %wheel ALL=(ALL) ALL
```

### [Login into newly created user](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#login-into-newly-created-user)

```
   # su - 0x
   $ xdg-user-dirs-update
```

### [Install AUR package manager](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#install-aur-package-manager)

In this guide we'll install [yay](https://github.com/Jguer/yay) as the AUR package manager. More about [AUR](https://aur.archlinux.org/).

TL;DR AUR is a Community-driven package repository.

```
   $ mkdir Sources
   $ cd Sources
   $ git clone https://aur.archlinux.org/yay.git
   $ cd yay
   $ makepkg -si
```

### [The coolest Pacman](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#the-coolest-pacman)

If you want to make Pacman look cooler you can edit the configuration file and uncomment the `Color` option and add just below the `ILoveCandy` option.

```
   $ sudo vim /etc/pacman.conf
```

### [PulseAudio applet](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#pulseaudio-applet)

If you want to manage your computer's volume from a small icon in the systray:

```
   $ yay -S pa-applet-git
```

### [Manage Bluetooth](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#manage-bluetooth)

```
   $ sudo pacman -S bluez bluez-utils blueman
   $ sudo systemctl enable bluetooth
```

### [Improve laptop battery consumption](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#improve-laptop-battery-consumption)

```
   $ sudo pacman -S tlp tlp-rdw powertop acpi
   $ sudo systemctl enable tlp
   $ sudo systemctl enable tlp-sleep
   $ sudo systemctl mask systemd-rfkill.service
   $ sudo systemctl mask systemd-rfkill.socket
```

### [Enable SSD TRIM](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#enable-ssd-trim)

```
   $ sudo systemctl enable fstrim.timer
```

### [Install some basic fonts](https://gist.github.com/fjpalacios/441f2f6d27f25ee238b9bfcb068865db#install-some-basic-fonts)

```
   $ sudo pacman -S noto-fonts ttf-ubuntu-font-family ttf-dejavu ttf-freefont
   $ sudo pacman -S ttf-liberation ttf-droid ttf-roboto terminus-font
```