# How to set-up `Ubuntu Server LTS`
## Download image `Ubuntu Server`
```shell
curl -L https://cdimage.ubuntu.com/ubuntu/releases/22.04.2/release/ubuntu-22.04.2-live-server-arm64.iso
```
```shell
wget https://cdimage.ubuntu.com/ubuntu/releases/22.04.2/release/ubuntu-22.04.2-live-server-arm64.iso
```

## Installation `Ubuntu Server`
* At boot time, select `Try or install Ubuntu Server`

After the startup, make the basic configurations by selecting *the system language* and the *keyboard configuration*.

* In the `Choose type of install` menu
  * Select `Ubuntu Server (minimized)`
  * Select in `Additional options` -> `Search for third-party drivers`

Once you have finished configuring your installation (disk, user, etc.), reboot the system.

## First Login

### Update and Upgrade your system
Make sure your packages are up to date. Sometimes you may get some errors when trying to install your desktop environment,
when you’re trying to install it on a fresh server, for example.

To do this run the following command to update the system’s package index and upgrade your packages.
```shell
sudo apt update && sudo apt upgrade --yes
```
After that do a system reboot

### Installing packages to get started
Do the minimal package installation to get started!
```shell
sudo apt install bash-completion nano tmux
```

### Additional option
* **Using multiarch**
    To enable the installation of multiarch binaries, `apt` and `dpkg` need configuration changes.
    For example, if you have an amd64 system that you want to install i386 libraries onto, do the following:
    ```shell
    sudo dpkg --add-architecture i386
    sudo apt update  # update to refresh the package cache with the newly added architecture
    ```
    To delete `i386` :
    ```shell
    sudo dpkg --remove-architecture i386
    ```

* **How to change welcome message (motd)**
    Modifying the `/etc/motd` file is fast and effective way on how to quickly change the welcome message. However,
    for more elaborate configuration it is recommend to customize the MOTD via scripts located within the `/etc/update-motd.d`

    Depending on your needs you can selectively disable one or more scripts by removing the executable permissions.
    For our example we will disable all scripts and create a new `10-help-text` script.

    Disable `10-help-text` current default MOTD’s help scripts :
    ```shell
    sudo chmod -x /etc/update-motd.d/10-help-text
    ```

### Install Desktop (KDE, Gnome, i3wm)
#### Install KDE desktop

Install KDE desktop
```shell
sudo apt install kde-plasma-desktop
```

Some defaults package for starter with KDE desktop:
```bash
sudo apt install konsole dolphin dolphin-plugins kate gwenview okular kompare kdiff3 kfind spectacle kcolorchooser kolourpaint kruler ark krunner krename kcalc
```
KDE is a community of friendly people who create over 200 apps which run on any Linux desktop,
and often other platforms too. [Here is the complete list](https://apps.kde.org/)

#### Install Gnome desktop
By default, the *Window Manager* is `wayland` if you want to change and select `X11` at login (Display Manager).
```shell
sudo apt install ubuntu-desktop-minimal
```

Some defaults pakage fot starter with Gnome desktop:
```shell
sudo apt install meld font-manager lollypop easytag gpick gitg mypaint gnome-shell-extensions
```
Discover the [best applications](https://apps.gnome.org/) in the GNOME ecosystem and learn how to get involved.

For the most used gnome-shell extensions [look here](https://github.com/PhineasPhreak/dotfiles/blob/master/packages/gnome-shell-extension.lst)

**optional**, custom [gnome-shell spacing](https://gist.github.com/PhineasPhreak/0fe00b4a967eb6f77e577cd0c71dab6e)

#### Install i3wm desktop

[Installation of i3 for Ubuntu/Archlinux](https://github.com/PhineasPhreak/dotfiles/blob/master/configs/i3wm/.config/i3/README.md)

For more applications for the Ubuntu distribution [look here](https://github.com/PhineasPhreak/dotfiles/blob/master/packages/pkg.lst)
