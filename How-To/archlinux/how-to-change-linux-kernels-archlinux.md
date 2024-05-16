# How to Change Linux Kernels on Arch Linux
In Arch Linux, the GRUB groups the kernels in a submenu, which means you will not see multiple kernel entries in the main grub menu.

However, you can find the kernel entries under the **Advanced options for Arch Linux** menu.

If you do not like this behavior and want the GRUB to list all the kernels under the single main menu, you will need to edit the `/etc/default/grub` file.

```shell
$EDITOR /etc/default/grub
```

Then, uncomment or update the below lines as shown below.

```shell
# Remember to boot again into the last Kernel you booted
GRUB_DEFAULT=saved

# Uncomment to make GRUB remember the last selection. This requires
# setting 'GRUB_DEFAULT=saved' above.
GRUB_SAVEDEFAULT=true

# Uncomment to disable submenus in boot menu
GRUB_DISABLE_SUBMENU=y
```

After making changes, regenerate the `grub.cfg` file with the below command.

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Then, reboot your system.

```shell
reboot
```

Now, you will see the kernel entries in the main GRUB menu for the selection. You can now boot into the kernel you want, and that kernel will be the default kernel for your system until you boot into some other kernel.
