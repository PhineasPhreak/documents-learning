# How to Install and Manage Multiple Kernels on Arch Linux
Arch Linux allows installing a wide variety of Linux kernels in addition to the stable kernel. You can install four [officially supported](https://wiki.archlinux.org/title/Kernel#Officially_supported_kernels) Linux kernels and eleven [unofficial kernels](https://wiki.archlinux.org/title/Kernel#Unofficial_kernels) per your requirements.

# Install Linux kernels on Archlinux
## Stable kernel
This kernel is the primary and stable kernel installed on your system by default.

`linux` Vanilla Linux kernel and modules, with a few patches applied.

```shell
sudo pacman -Sy linux linux-headers
```

## LTS kernel
This kernel is long-term-supported and will be supported until the end of life (EOF)

`linux-lts` Long-term support (LTS) Linux kernel and modules with configuration options targeting usage in servers.

```shell
sudo pacman -Sy linux-lts linux-lts-headers
```

## Hardened kernel
This kernel mainly focuses on security parameters by applying hardening patches to mitigate kernel and userspace exploits.

`linux-hardened` A security-focused Linux kernel applying a set of hardening patches to mitigate kernel and userspace exploits. It also enables more upstream kernel hardening features than `linux`.

```shell
sudo pacman -Sy linux-hardened linux-hardened-headers
```

## ZEN kernel
This kernel provides better performance than the stable kernel.

`linux-zen` Result of a collaborative effort of kernel hackers to provide the best Linux kernel possible for everyday systems.

```shell
sudo pacman -Sy linux-zen linux-zen-headers
```

# Post-Installation
After installing the Linux kernels on Arch Linux, regenerate the `grub.cfg` file with the below command.
```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Then, reboot your system.

```shell
reboot
```

# Boot Right Linux Kernel on Arch Linux
In the Arch Linux GRUB Menu, select **Advanced options for Arch Linux** and then press enter.

By default, the GRUB groups installed kernels in a submenu, which is why you will not be able to see multiple kernel entries in the main grub menu itself.

Now, you can choose the kernel you want your system to boot from.

# Remove Linux Kernels on Arch Linux
In case you do not wish to have specific kernels, use the `pacman` command to remove them.

```shell
sudo pacman -Rsu linux linux-headers # Stable kernel
```
```shell
sudo pacman -Rsu linux-lts linux-lts-headers # LTS kernel
```
```shell
sudo pacman -Rsu linux-hardened linux-hardened-headers # Hardened kernel
```
```shell
sudo pacman -Rsu linux-zen linux-zen-headers # Zen kernel
```

After removing the Linux kernels on Arch Linux, regenerate the `grub.cfg` with the below command.

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Then, reboot your system.

```shell
reboot
```
