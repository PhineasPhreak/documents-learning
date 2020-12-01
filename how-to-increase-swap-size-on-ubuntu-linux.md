# How to Increase Swap Size on Ubuntu Linux

The recent releases of [Ubuntu](https://ubuntu.com/) use [swap file](https://help.ubuntu.com/community/SwapFaq) instead of the traditional swap partition. 
The swap file is simply a file under the root which is used as swap to share the burden on the RAM.

The biggest advantage of using a swap file is that you can easily resize it. That’s not always the case when you use a dedicated swap partition.

Let’s see how to resize the swap space on Ubuntu.

## Increase swap size on Ubuntu

If you are using swap partition and want to increase the swap size, you can [create swap file](https://itsfoss.com/create-swap-file-linux/). Your Linux system can use multiple swap spaces as needed. This way, you won’t have to touch the partition.

This tutorial assumes that you are using swap file on your system, not a swap partition.

Now, let’s see how to increase the swap file. First thing first, make sure that you have a swap file in your system.
```console
swapon --show
```

It will show the current swap available. If you see the type file, it indicates that you are using a swap file.
```console
swapon --show
NAME      TYPE SIZE USED PRIO
/swapfile file   2G   0B   -2
```

Now before you resize the swap file, you should turn the swap off. You should also make sure that you have enough free RAM available to take the data from swap file. Otherwise, create a temporary swap file.

You can [disable a given swap file](https://web.mit.edu/rhel-doc/5/RHEL-5-manual/Deployment_Guide-en-US/s1-swap-removing.html) using this command. The ***command doesn’t produce any output and it may take a few minutes to complete***:
```console
sudo swapoff /swapfile
```

Now [use the fallocate command in Linux](https://man7.org/linux/man-pages/man1/fallocate.1.html) to change the size of the swap file.
```console
sudo fallocate -l 4G /swapfile
```

Make sure that you mark this file as swap file:
```console
sudo mkswap /swapfile
```

You should see an output like this where it warns you that old swap signature is being wiped out.
```console
sudo mkswap /swapfile
mkswap: /swapfile: warning: wiping old swap signature.
Setting up swapspace version 1, size = 4 GiB (4294967296 bytes)
no label, UUID=c50b27b0-a530-4dd0-9377-aa28eabf3957
```

Once you do that, enable the swap file:
```console
sudo swapon /swapfile
```

That’s it. You just increased the swap size in Ubuntu from 2 GB to 4 GB.</br>
You can [check swap size](https://www.cyberciti.biz/faq/linux-check-swap-usage-command/) using the [free command](https://man7.org/linux/man-pages/man1/free.1.html) or the `swapon --show` command.
```console
free -h
              total        used        free      shared  buff/cache   available
Mem:           7.7G        873M        5.8G        265M        1.0G        6.3G
Swap:          4.0G          0B        4.0G
```

You see how easy it is to resize swap size thanks to the swap files. You didn’t touch the partition, you didn’t reboot the system. Everything was done on the fly. How cool is that!

I hope you found this quick tutorial helpful in resizing the swap space on Ubuntu as well as other Linux distributions.