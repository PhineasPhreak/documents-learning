# ArchLinux Installation *(16/05/2024)*
**A simple, lightweight distribution**

You've reached the website for Arch Linux, a lightweight and flexible Linux® distribution that tries to Keep It Simple.

**Requirements** :

* Site : [archlinux.org/](https://archlinux.org/)

* Download : [archlinux.org/download/](https://archlinux.org/download/)

* Installation Guide : [wiki.archlinux.org/title/Installation_guide/](https://wiki.archlinux.org/title/Installation_guide)

* General Recommendations : [wiki.archlinux.org/title/General_recommendations](https://wiki.archlinux.org/title/General_recommendations)

* wiki : [wiki.archlinux.org/](https://wiki.archlinux.org/)

* AUR : [aur.archlinux.org/](https://aur.archlinux.org/) | Go to [install **yay** on Archlinux](#installing-yay-aur-helper-in-archlinux)

    > Welcome to the AUR! Please read the AUR User Guidelines and [AUR TU Guidelines](https://wiki.archlinux.org/index.php/AUR_User_Guidelines) for more information. Contributed PKGBUILDs must conform to the [Arch Packaging Standards](https://wiki.archlinux.org/index.php/Arch_Packaging_Standards) otherwise they will be deleted! Remember to vote for your favourite packages! Some packages may be provided as binaries in [community].
    >
    > DISCLAIMER: AUR packages are user produced content. Any use of the provided files is at your own risk.

# Installation
After the installer decompresses and loads the `Linux Kernel` you will be automatically thrown to an Arch Linux Bash terminal (__TTY__) with root privileges

You can also use [archinstall](https://archinstall.archlinux.page/) is a helper library which automates the installation of [Arch Linux](https://wiki.archlinux.org/title/Arch_Linux). It is packaged with different pre-configured installers, such as a "guided" installer.

How to create an [Arch Linux Installer USB drive](https://wiki.archlinux.org/title/USB_flash_installation_medium) (also referred to as "flash drive", "USB stick", "USB key", etc) for booting in BIOS and UEFI systems.

## Configuration keyboard
The default keyboard layout in the live session is US. While most English language keyboards will work just fine, the same cannot be true for French, German and other keyboards.

If you face difficulty, you can list out all the supported keyboard layout :
```bash
ls /usr/share/kbd/keymaps/**/*.map.gz  # list out all the supported keyboard layout
```

And then change the layout to the an appropriate one using `loadkeys` command. For example, if you want French keyboard, this is what you’ll use :

```bash
loadkeys fr-latin1  # choose french keyboard
```

## Check if you have UEFI mode enabled
Some steps are different for UEFI and non-UEFI systems.You should verify if you have UEFI enabled system or not. Use this command :
```bash
ls /sys/firmware/efi/efivars  # verify if you have UEFI enabled system or not
```
```bash
efivar -L  # tools and libraries to work with EFI variables
```
**If this directory exists, you have a UEFI enabled system.**

## ArchLinux Network for Arch Live media
* [**Wired**](https://wiki.archlinux.org/title/Network_configuration) connection:

    Check ArchLinux Network
    ```bash
    ip a  # show all interface "ip link" or "ip addr"
    ping -c5 google.com  # test your connection
    ```

* [**Wifi**](https://wiki.archlinux.org/title/Iwd) connection:

    - To get an interactive prompt do:
    ```bash
    iwctl
    ```

    - Connect to a network:
        First, if you do not know your wireless device name, list all Wi-Fi devices:
        ```bash
        [iwd]: device list  # show interface like "wlan0" or something
        ```
        Then, to scan for networks:
        ```bash
        [iwd]: station <device> scan
        ```
        You can then list all available networks:
        ```bash
        [iwd]: station <device> get-networks
        ```
        Finally, to connect to a network:
        ```bash
        [iwd]: station <device> connect <SSID>
        ```
        **If a passphrase is required, you will be prompted to enter it**. Alternatively, you can supply it as a command line argument:
```bash
iwctl --passphrase <passphrase> station <device> connect <SSID>
```

## Disk OS detection, Partitions and format Layout
We’ll start to configure the Hard Disk partitions. For this stage you can run `cfdisk`, `cgdisk`, parted or `gdisk` utilities to perform a disk partition layout for a GPT disk.
```bash
lsblk  # show your disk with partitions
```

### OS detection
If, you are OS on yout disk use `gdisk` to zap data structure.

```bash
gdisk -l /dev/sda  # show partion and follow to destroy it
```
Press `x` for extra functionality, after that press `z` for zap data structure.

Here's a table with some handy `gdisk` commands, for destroy GPT structure and blank out MBR.

| Command | Description                               |
| :-----: | ----------------------------------------- |
| x       | extra functionality (expert only)         |
| z       | zap (destroy) GPT data structure and exit |
|||
| p       | Print partitions table                    |
| d       | Delete partition                          |
| w       | Write partition                           |
| q       | Quit                                      |
| ?       | Help                                      |

### Creation of the partitions
Personally, i use `cgdisk`
```bash
cgdisk /dev/sda  # start partitioning /dev/sda disk
```

For a basic partition, the layout table uses the following structure. For that example, I'll create **3 or 4 partitions**, described on the following table:

* `Boot` Partition :
    - EFI System partition (Code: *EF00*) (/dev/sda1) with 512MiB size, FAT32 formatted.

    ***OR***

    - BIOS System partition (Code: *EF02*) (/dev/sda1) with 512MiB size, FAT32 formatted.

* `Swap` partition **or** [`swapfile`](#swapfile-creation) (Code: *8200*)(/dev/sda2) with 2xRAM recommended size, Swap On.
* `Root` partition (Code: *8300*)(/dev/sda3) with at least 35GiB size or rest of HDD space, ext4 formatted.
* `Home` partition (Code: *8300*)(/dev/sda4) with the rest of HDD space, ext4 formatted.

You can also review the partition table summary
```bash
fdisk -l /dev/sda  # review the partition table summary
```

### Format partitions
It’s time to format the partitions with the required file systems. Issue the following commands to create a `FAT32` file system for `EFI System` or `BIOS System` partition (/dev/sda1), to create the `EXT4` file system for the root partition (/dev/sda3) and the home parttion (/dev/sda4) and create the `swap` partition for /dev/sda2.

```bash
mkfs.fat -F32 /dev/sda1  # create a FAT32 file system for EFI System partition or BIOS partion
mkfs.ext4 /dev/sda3  # create the EXT4 file system for the root partition
mkfs.ext4 /dev/sda4  # create the EXT4 file system for the home partition
mkswap /dev/sda2  # create the swap partition
swapon /dev/sda2  # the swap partition needs to be initialized
```

## Mount Partitions
In order to install Arch Linux, the `/(root)` partition must be mounted to `/mnt` directory mount point in order to be accessible.
```bash
mount /dev/sda3 /mnt
mkdir /mnt/home
mount /dev/sda4 /mnt/home
#mkdir /mnt/boot  # create a folder only if you use the systemd like Bootloader
#mount /dev/sda1 /mnt/boot  # only if you use systemd like Bootloader
```

Verity the mountpoint with `lsblk`.

```bash
lsblk  # check the mountpomit for root partition, home partition and swap partition
```

## Select an appropriate mirror
You can edit `/etc/pacman.d/mirrorlist` file and select the closest mirror website on top of the mirror file list.

```bash
cp -v /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup  # make backup
```

* **Reflector** too that you can use to list the fresh and fast mirrors located in your country.
    ```bash
    reflector --list-countries  # display a table of the distribution of servers by country
    ```

    Now, get the good mirror list with reflector and save it to mirrorlist. You can change the country from US to your own country.

    ```bash
    reflector -c "US" --fastest 12 --latest 10 --number 12 --protocol https --completion-percent 100 --sort rate --save /etc/pacman.d/mirrorlist
    ```

* **Rankmirror** same thing than reflector, but this is the old way.
    ```bash
    pacman -Sy pacman-contrib  # update and install rankmirror
    ```
    ```bash
    rankmirror -n 8 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist  # generate mirrorlist
    ```

You can also enable **Arch Multilib** support for the live system by uncommenting the following lines from `/etc/pacman.conf` file.

```properties
[multilib]
Include = /etc/pacman.d/mirrorlist
```

After that, Update pacman for multilib
```bash
pacman -Syy
```

## Install ArchLinux
Next, start installing Arch Linux by issuing the following command.
```bash
pacstrap -K -i /mnt base base-devel linux linux-headers linux-firmware nano bash-completion
```
> **Options pacstrap:**
> -K : Initialize an empty pacman keyring in the target (implies -G).
> -i : Prompt for package confirmation when needed (run interactively).
> **Packages:**
> base : Minimal package set to define a basic Arch Linux installation
> base-devel : 	Basic tools to build Arch Linux packages
> linux : The Linux kernel and modules or others like (linux-lts, linux-zen)
> linux-headers : Headers and scripts for building modules for the Linux kernel
> linux-firmware : Firmware files for Linux
> nano : Pico editor clone with enhancements
> bash-completion : Programmable completion for the bash shell

To install the LTS kernel and Linux LTS headers : `linux-lts` and `linux-lts-headers`.
For more information see [Officially supported kernels](https://wiki.archlinux.org/title/kernel#Officially_supported_kernels)

After the installation completes, generate `fstab` file for your new Arch Linux system by issuing the following command.

```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
```
Subsequently, inspect fstab file content by running the below command.
```bash
cat /mnt/etc/fstab
```

## ArchLinux System Configuration
### Network configuration
In order to further **configure Arch Linux, you must chroot into `/mnt`** the system path and add a hostname for your system by issuing the below commands.
```bash
arch-chroot /mnt  # Arch Linux system on the disk.
echo "arch" > /etc/hostname  # add a hostname for your system
cat /etc/hostname  # check hostname
```

And edit this `/etc/hosts` file with Vim or Nano editor to add the following lines to it
```configure
127.0.0.1   localhost
::1     localhost
127.0.1.1   myhostname.localdomain  myhostname
```

### Solid state drive - TRIM support
Most SSDs support the `ATA_TRIM command` for sustained long-term performance and wear-leveling.
> **Warning: Users need to be certain that their SSD supports TRIM before attempting to use it. Data loss can occur otherwise!**

To verify TRIM support, run:
```bash
lsblk --discard  # if DISC-GRAN and DISC-MAX = Non-zero values indicate TRIM support
```

And check the values of DISC-GRAN (discard granularity) and DISC-MAX (discard max bytes) columns. Non-zero values indicate TRIM support.

Alternatively, install `hdparm` package and run:

```bash
hdparm -I /dev/sda | grep TRIM
    *    Data Set Management TRIM supported (limit 8 block)
```

The [`util-linux`](https://archlinux.org/packages/?name=util-linux) package provides `fstrim.service` and `fstrim.timer` systemd unit files. Enabling the timer will activate the service weekly. The service executes fstrim(8) on all mounted filesystems on devices that support the discard operation.

```bash
systemctl enable fstrim.timer
```

### Setting up Locale
Next, **configure your system Language**. Choose and uncomment your preferred encoding languages from `/etc/locale.gen` file then set your locale by running the following commands.

```bash
nano /etc/locale.gen
```

the locale.gen file excerpt:

```bash
en_US.UTF-8 UTF-8
en_US ISO-8859-1
```

Generate your system language layout.

```bash
locale-gen  # Generate your system language layout
echo LANG=en_US.UTF-8 > /etc/locale.conf  # Configuration file for locale settings
export LANG=en_US.UTF-8
```

### Setting Timezone
The next step is to **configure your system time zone** by creating a symlink for your sub time zone (`/usr/share/zoneinfo/Continent/Main_city`) to `/etc/localtime` file path.

```bash
ls /usr/share/zoneinfo/  # listing time zone by continent
ln -s /usr/share/zoneinfo/Europe/Paris /etc/localtime  # creating a symlink for your sub time zone
```

You should also **configure the hardware clock to use UTC** (the hardware clock is usually set to the local time).
```bash
hwclock --systohc --utc  # configure the hardware clock to use UTC
```

### Enable Arch Multilib
Like many famous Linux distributions, Arch Linux uses repo mirrors for different world locations and multiple system architectures.

The standard repositories are enabled by default, but if you want **to activate Multilib repositories you must uncomment `[multilib]` directives from `/etc/pacman.conf`** file, as shown in the below excerpt.

```properties
[multilib]
Include = /etc/pacman.d/mirrorlist
```

After the repository file has been edited, synchronize and update database mirrors and packages by running the below command.

```bash
pacman -Syu  # synchronize and update database mirrors and packages
```

### Set up `root` and `user` passwd
Next, `set up a password for the root account` and `create a new user with Sudo privileges` in the Arch box by issuing the commands below.
```bash
passwd  # set up a password for the root account

# create new user in groups and with bash like shell
useradd -mg users -G wheel,storage,power -s /bin/bash your_new_user
passwd your_new_user  # set up a password for the user account
```

* Optional :
    Also, expire the user password in order to force the new user to change the password at first login.

    ```bash
    chage -d 0 <your_new_user>
    ```

### Enable Sudo Privileges *(sudoers/visudo)*
After the new user has been added you need to install the sudo package and `update the wheel group` line from `/etc/sudoers` file in order to grant root privileges to the newly added user.

Open `/etc/sudoers` file with EDITOR:
```bash
EDITOR=vim visudo
```

uncomment this line to `/etc/sudoers` file:
```properties
%wheel ALL=(ALL) ALL
```

Add this line at bottom to `/etc/sudoers` file and save/quit:
```properties
Defaults rootpw  # You are asked for the root password if you use the sudo command
```
> **Reduce the number of times you have to type a password**
>
>If you are annoyed that you have to **re-enter your password every 5 minutes (default)**, you can change this by setting a longer value for `timestamp_timeout` (in minutes):
> ```properties
> Defaults timestamp_timeout=10
> ```


### Install Grub bootloader
This is one of the crucial steps and it differs for **`UEFI`** and **`non-UEFI`** systems.

Let me show it for the UEFI systems first.

* **UEFI**

    **Make sure that you are still using arch-chroot**. Install required packages:
    ```bash
    pacman -S grub os-prober efibootmgr  # install required packages for UEFI system
    ```
    Create the directory where EFI partition will be mounted:
    ```bash
    mkdir /boot/efi
    ```
    Now, mount the [ESP](https://wiki.archlinux.org/title/EFI_system_partition) partition you had created
    ```bash
    mount /dev/sda1 /boot/efi
    ```
    Install grub like this:
    ```bash
    grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
    ```
    One last step, generate the main configuration file `/boot/grub/grub.cfg`:
    ```bash
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

* **non-UEFI**

    **Make sure that you are still using arch-chroot**. Install `grub` package first:
    ```bash
    pacman -S grub os-prober  # install just grub required packages for non-UEFI system
    ```
    And then install grub like this (don’t put the disk number sda1, just the disk name sda):
    ```bash
    grub-install --target=i386-pc /dev/sda
    ```
    One last step, generate the main configuration file `/boot/grub/grub.cfg`:
    ```bash
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

**If error: will not proceed with blocklists**
You need to use the `--force` option to allow usage of blocklists.
Without `--force` you may get the below error and grub-setup will not setup its boot code in the partition boot sector:
```bash
/sbin/grub-setup: error: will not proceed with blocklists
```
With `--force` you should get:
```bash
Installation finished. No error reported.
```
For more see [GRUB/Tips and tricks](https://wiki.archlinux.org/title/GRUB/Tips_and_tricks)

### Configure DHCPCD
This article describes how to configure network connections on OSI layer 3 and above. Medium-specifics are handled in the `/Ethernet` and [`/Wireless`](https://wiki.archlinux.org/title/Network_configuration/Wireless) subpages.

Install the `dhcpcd` package.
```bash
pacman -S dhcpcd
```
* To start the daemon for **all network interfaces**, `start`/`enable` `dhcpcd.service`.
    Issuing the below commands.
    ```bash
    systemctl start dhcpcd.service
    systemctl enable dhcpcd.service
    ```

* To start the daemon for a **specific interface** alone, `start`/`enable` the template unit `dhcpcd@interface.service`

    Both wired and wireless interface names can be found via ls `/sys/class/net` or `ip link`. Note that `lo` is the virtual loopback interface and not used in making network connections.

    To show the status of the interfaces:
    ```bash
    ip link
    ```

    To check the status of the interface `ens33` or `enp2s1`:
    ```properties
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master br0 state DOWN mode DEFAULT qlen 1000
    ...
    ```

    The `UP` in `<BROADCAST,MULTICAST,UP,LOWER_UP>` is what indicates the interface is up, not the later `state DOWN`.

    Issuing the below commands.
    ```bash
    systemctl start dhcpcd@<interface>.service  # interface: ens33 or enp2s1 maybe
    systemctl enable dhcpcd@<interface>.service
    ```

### Configure NetworkManager
[NetworkManager](https://wiki.archlinux.org/title/NetworkManager) is a program for providing detection and configuration for systems to automatically connect to networks. NetworkManager's functionality can be useful for both wireless and wired networks.

Install the networkmanager package.
```bash
pacman -S networkmanager
```

You will need to enable `NetworkManager.service`:
```bash
systemctl enable NetworkManager.service
```

### Swapfile creation
You probably have noticed that I have not created a Swap partition. This is because I recommend to use a Swap file. Let’s [create a swap file for this Arch Linux](https://wiki.archlinux.org/title/swap#Swap_file_creation) installation too.

Create a Swap file of 8G or whatever your RAM size is:
```bash
fallocate -l 8G /swapfile
```

Change its access rules, format and enable it:
```bash
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

Also, add this Swap file to the `/etc/fstab`:
```bash
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

And check if the Swap file is working:
```bash
free -m
swapon --show
```

### Driver CPU and GPU
1. **Install CPU [Microcode](https://wiki.archlinux.org/title/Microcode)**

    - For `Intel` processors:
        ```bash
        pacman -S intel-ucode
        ```
    - For `AMD` processors:
        ```bash
        pacman -S amd-ucode
        ```

2. **Install _NVIDIA_ driver**

This article covers the proprietary [NVIDIA](https://wiki.archlinux.org/title/NVIDIA) graphics card driver.

These instructions are for those using the stock [linux](https://archlinux.org/packages/?name=linux) or [linux-lts](https://archlinux.org/packages/?name=linux-lts) packages. For custom kernel setup, skip to the [next](https://wiki.archlinux.org/title/NVIDIA#Custom_kernel) subsection.

> The `nvidia` package (for use with the `linux` kernel) **or** `nvidia-lts` (for use with the `linux-lts` kernel) package.

* **Install** the `nvidia-dkms` package (or a specific branch). The Nvidia module will be rebuilt after every Nvidia or kernel update thanks to the DKMS.

* **Install** NVIDIA drivers utilities : `nvidia-utils` and `lib32-nvidia-utils`

* **Install** OpenCL implemention for NVIDIA : `opencl-nvidia` and `lib32-opencl-nvidia`

* **Install** The GL Vendor-Neutral Dispatch library : `libglvnd` and `lib32-libglvnd`

* **Install** Tool for configuring the NVIDIA graphics driver : `nvidia-settings`

* To avoid the possibility of forgetting to update [initramfs](https://wiki.archlinux.org/title/Initramfs) after an NVIDIA driver upgrade, you may want to use a [pacman hook](https://wiki.archlinux.org/title/NVIDIA#Pacman_hook).

* [Hardware accelerated video decoding](https://wiki.archlinux.org/title/NVIDIA#Hardware_accelerated_video_decoding)

* [Hardware accelerated video encoding with NVENC](https://wiki.archlinux.org/title/NVIDIA#Hardware_accelerated_video_encoding_with_NVENC)

Conclusion :
```bash
pacman -S nvidia-dkms nvidia-utils lib32-nvidia-utils opencl-nvidia lib32-opencl-nvidia libglvnd lib32-libglvnd nvidia-settings
```

3. **Install _AMD_ driver**

[AMDGPU](https://wiki.archlinux.org/title/AMDGPU) is the open source graphics driver for AMD Radeon graphics cards from the Graphics Core Next family.

Install the `mesa` package, which provides the DRI driver for 3D acceleration.

* For 32-bit application support, also install the `lib32-mesa` package from the [`multilib`](#enable-arch-multilib) repostory.

* For the DDX driver (which provides 2D acceleration in Xorg), install the `xf86-video-amdgpu` package.

* For [Vulkan](https://wiki.archlinux.org/title/Vulkan) support, install the `vulkan-radeon` or `amdvlk` package. Optionally install the `lib32-vulkan-radeon` or `lib32-amdvlk` package for 32-bit application support.

Support for [accelerated video decoding](https://wiki.archlinux.org/title/AMDGPU#Video_acceleration) is provided by `libva-mesa-driver` and `lib32-libva-mesa-driver` for VA-API and `mesa-vdpau` and `lib32-mesa-vdpau` packages for VDPAU.

Conclusion :
```bash
pacman -S mesa lib32-mesa xf86-video-amdgpu vulkan-radeon amdvlk lib32-vulkan-radeon lib32-amdvlk libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau
```

4. **Install _Intel_ graphics**

Since Intel provides and supports open source drivers, Intel graphics are essentially plug-and-play.

```bash
# See https://wiki.archlinux.org/title/intel_graphics#Installation
pacman -S mesa lib32-mesa xf86-video-intel
```

### Install display server
`mesa` is an open-source implementation of the OpenGL specification.
```bash
pacman -S --needed mesa lib32-mesa
```

[Xorg](https://wiki.archlinux.org/title/xorg) can be installed with the `xorg-server` package. Additionally, some packages from the `xorg-apps` group are necessary for certain configuration tasks, they are pointed out in the relevant sections.

Finally, an `xorg` group is also available, which includes Xorg server packages, packages from the `xorg-apps` group and fonts.

```bash
pacman -S xorg-server xorg-apps xorg-xinit xorg-twm xorg-xclock xterm
```

### Install a desktop environment
1. A [desktop environment](https://wiki.archlinux.org/title/Desktop_environment) (DE) is an implementation of the [desktop metaphor](https://en.wikipedia.org/wiki/Desktop_metaphor) made of a bundle of programs, which share a common graphical user interface (GUI).

    Before installing `Plasma`, **make sure you have a working Xorg installation on your system**.

    Install the [`plasma-meta`](https://archlinux.org/packages/?name=plasma-meta) meta-package or the [`plasma`](https://archlinux.org/groups/x86_64/plasma/) group. For differences between `plasma-meta` and `plasma` reference Package group. Alternatively, for a more minimal Plasma installation, install the [`plasma-desktop`](https://archlinux.org/packages/?name=plasma-desktop) package.

    In my case i use `plasma`:
    ```bash
    pacman -S plasma
    ```

    For the installation of other desktop environments, here is a non-exhaustive list:

    - To install [GNOME](https://wiki.archlinux.org/title/GNOME): `gnome` `gnome-extra`

    - To install [Cinnamon](https://wiki.archlinux.org/title/Cinnamon): `cinnamon` `nemo-fileroller`

    - To install [XFCE](https://wiki.archlinux.org/title/Xfce): `xfce4` `xfce4-goodies`

    - To install [MATE](https://wiki.archlinux.org/title/MATE): `mate` `mate-extra`

    - To install [Deepin](https://wiki.archlinux.org/title/Deepin_Desktop_Environment): `deepin` `deepin-extra`

    - To install [Budgie](https://wiki.archlinux.org/title/Budgie): `budgie-desktop` `budgie-extras`

    - To install [i3-wm](https://wiki.archlinux.org/title/i3): `i3-wm` `i3lock` `i3status` `i3blocks` `dmenu`

    - Install packages with [AUR](https://aur.archlinux.org/packages/yay):
      - To install [JWM](https://wiki.archlinux.org/title/JWM): *With the [AUR](https://aur.archlinux.org/)* jwm

2. A [display manager](https://wiki.archlinux.org/title/Display_manager), or login manager, is typically a graphical user interface that is displayed at the end of the boot process in place of the default shell.
    ```bash
    pacman -S sddm
    ```

    To start `SDDM` at boot time enable `sddm.service`.
    ```bash
    systemctl enable sddm.service
    ```

    For the installation of other **graphical** display manager, here is a non-exhaustive list:

    - To install KDE Plasma: [`SDDM`](https://wiki.archlinux.org/title/SDDM)

    - To install GNOME: [`GDM`](https://wiki.archlinux.org/title/GDM)

    - To install XFCE: [`lightdm`](https://wiki.archlinux.org/title/LightDM) `lightdm-gtk-greeter-settings`

    - To install LXDM: [`LXDM`](https://wiki.archlinux.org/title/LXDM)

### END
**Arch Linux is now installed and configured**
```bash
exit  # exit the chroot environment
umount -R /mnt  # unmount the partitions
reboot # reboot the system
```

After, reboot check all `systemctl` service:
```bash
systemctl --failed  # check service failed after boot, if 0 loaded units listed it's OKAY
```

---

# Post-installation
See [General recommendations](https://wiki.archlinux.org/title/General_recommendations) for system management directions and post-installation tutorials (like setting up a graphical user interface, sound or a touchpad).

For a list of applications that may be of interest, see [List of applications](https://wiki.archlinux.org/title/List_of_applications).

* For audio package installetion:
```bash
sudo pacman -S pulseaudio pulseaudio-alsa pavucontrol
```

* Some defaults package for starter with KDE desktop:
```bash
sudo pacman -S konsole dolphin dolphin-plugins firefox kate gwenview p7zip tar zip unzip curl wget neofetch okular kompare kfind spectacle kcolorchooser kruler ark

# For more stuff
sudo pacman -S  yakuake vlc mpv ffmpeg juk htop tmux xarchiver flameshot iwd
```

# Installing Yay (AUR) in ArchLinux
![Arch User Repository](https://wiki.archlinux.org/title/Arch_User_Repository) is a community-driven repository for Arch users, and packages are distributed in the form of PKGBUILD. Since the packages are in PKGBUILD form, you can not install them with Pacman. So, to install packages from AUR, you will need to perform a ![manual build](https://wiki.archlinux.org/title/Arch_User_Repository#Installing_and_upgrading_packages) to install the package or use an ![AUR helper](https://wiki.archlinux.org/title/AUR_helpers#Comparison_tables) to automate the package installation.

Yay (Yet Another Yogurt) – An AUR Helper Written in Go for Arch Linux distributions. The AUR helpers help to automate the usage of the Arch User Repository in the like searching packages published on the AUR, resolving dependencies, downloading, and building AUR packages.

L'objectif du dépôt de paquetages [`[archlinuxfr]`](https://wiki.archlinux.fr/depot_archlinuxfr) est de rassembler les paquetages des contributeurs de la communauté francophone.

## Steps to install *Yay* on ArchLinux from the source

1. Update your system

```bash
sudo pacman -Syyu
```

2. Install [Git](https://archlinux.org/packages/extra/x86_64/git/) and development tools to install Yay on Arch Linux **as the root user**.

```bash
sudo pacman -S --needed git base-devel
```

3. Then, download the AUR helper [yay](https://aur.archlinux.org/packages/yay) repository package with the `git` command.

```bash
git clone https://aur.archlinux.org/yay.git
```

4. And then, go to the downloaded directory.

```bash
cd yay
```

5. Finally, build the Yay AUR helper with the below command.

```bash
makepkg -si
```

6. Test it by installing a package

```bash
yay -S brave-bin  # install the brave browser
```

> Interestingly, **Yay will warn you against running as root or sudo**. It seems to work fine as an unprivileged user. I haven’t looked into why this is.

## How to use Yay on Arch Linux
The Yay AUR helper is similar to Pacman, and you will not find any difficulty in using it for installing packages from AUR.

`yay -Sy <package_name>` Install a package from AUR after synchronizing a remote repository

`yay <package_name>` Package search with the installation menu

`yay -Si <package_name>` View the package information

`yay -R <package_name>` Remove an installed package

`yay -Q` List the locally installed packages

`yay -Q <package_name>` Search for an installed package

`yay -Qi <package_name>` View the installed package’s information

`yay` Alias to `yay -Syu`. Perform system upgrade

`yay -Ps` Print system statistics like Yay version, statistics of installed packages

`yay -Yc` Clean unneeded dependencies

`yay -Y --gendb` Generate the package database of AUR packages that you installed without `yay` AUR helper

`yay -Syu --devel` Perform system upgrades, including AUR packages

`yay -Y --devel --save` Enable AUR package updates permanently. `yay` or `yay -Syu` will update AUR packages as well during the system upgrade.

`man yay` **Read Yay’s official manual**.
