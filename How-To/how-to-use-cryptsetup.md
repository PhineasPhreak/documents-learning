# Cryptsetup *(version : 2.4.3)*
> * You can also use old <action> syntax aliases :
>     * open: create (plainOpen), luksOpen, loopaesOpen, tcryptOpen
>     * close: remove (plainClose), luksClose, loopaesClose, tcryptClose

There are plenty of reasons why people would need to encrypt a partition. Whether they’re rooted it privacy, security,
or confidentiality, setting up a basic encrypted partition on a Linux system is fairly easy.
This is especially true when using LUKS (Linux Unified Key Setup), since its functionality is built directly into the kernel.

Let’s learn about encrypting partitions with LUKS Disk encryption is a method of protecting confidential data and sensitive data on any storage device
by converting the data into unreadable text (encrypting) such that only authorized users can decrypt and read the original data.

This method of protecting data is much stronger than simply having a password on your laptop since it actually changes the data itself instead of just putting it behind a password.


## What is LUKS?
Linux Unified Key Setup (LUKS) is a disk encryption specification released in 2004 for Linux.

Since LUKS is a standard method and not an external software, its implementation is uniform across all distros, partitions, and even other block devices such a USB drives.

It uses multiple ciphers such as `aes-cbc-essiv:sha256` and `aes-xts-plain` to encrypt the data. The specific cipher depends on the use case.


## Step-By-Step Encrypting Partitions With LUKS
We will be implementing LUKS using the cryptsetup command. As an example, I will be encrypting my USB Drive. But this method can be used on any empty partition in Linux.

> NOTE: Make sure you don’t have any data in the partition, USB you are going to encrypt as all the data will be lost in the process


## Installing Cryptsetup
### Debian/Ubuntu
On both Debian and Ubuntu, the **cryptsetup** utility is easily available in the repositories.
The same should be true for Mint or any of their other derivatives.
```shell
sudo apt-get install cryptsetup
```


### CentOS/Fedora
Again, the required tools are easily available in both CentOS and Fedora. These distributions break them down into multiple packages,
but they can still be easily installed using `yum` and `dnf` respectively.
* CentOS :
  ```shell
  yum install crypto-utils cryptsetup-luks cryptsetup-luks-devel cryptsetup-luks-libs
  ```
* Fedora :
  ```shell
  dnf install crypto-utils cryptsetup cryptsetup-luks
  ```


### OpenSUSE
OpenSUSE is more like the Debian based distributions, including everything that you need with **cryptsetup**.
```shell
zypper in cryptsetup
```


### Arch Linux
Arch stays true to its “keep it simple” philosophy here as well.
```shell
pacman -S cryptsetup
```


## Step 1: Identify the partition to be formatted.
You can list all filesystems using the following command.
```shell
df -hl
```

Since my USB drive is mounted at `/dev/xxx1`, I will be formatting that partition. If you formatting a primar hard drive partition this is usually something like `/dev/xxxX`


## Step 2: Unmount the partition
```shell
sudo unmount /dev/xxx1
```
Replace `/dev/xxx1` with the name of your partition which we identified in the last step.


## Step 3 : Format the partition
> DO NOT RUN UNTIL YOU HAVE MADE SURE THAT THE PARTITION DOES NOT HAVE ANY IMPORTANT DATA
```shell
sudo wipefs -a /dev/xxx1
```
**WARNING:** The following will erase all data on the partition being used and will make it unrecoverable. Proceed with caution.


## Step 4 : Format the partition with LUKS
Now we will use Cryptsetup on this formatted partition to make an encrypted LUKS partition. To do so, run the following

* So, a basic command with no options would look like the line below.
    ```shell
    sudo cryptsetup --verbose --verify-passphrase luksFormat /dev/xxx1
    ```
    > --verbose: Print more verbose messages.
    > --verify-passphrase: query  for  passwords  twice. Useful when creating a (regular) mapping for the first time, or when running luksFormat.

    After running this, you will be asked a passphrase. That passphrase is how you will access the device whenever you want. So make sure to remember the passphrase.

* The basic options are as follows:
    > --cipher:  This determines the cryptographic cypher used on the partition.  The default option is aes-xts-plain64
    >
    > --key-size: The length of the key used.  The default is 256
    >
    > --hash: Chooses the hash algorithm used to derive the key.  The default is sha256.
    >
    > --timeout: The time used for passphrase processing.  The default is 2000 milliseconds.
    >
    > --use-random/--use-urandom: Determines the random number generator used.  The default is --use-random.

    Obviously, you’d want to use the path to whichever partition that you’re encrypting. If you do want to use options, it would look like the following.
    ```shell
    cryptsetup --verbose --verify-passphrase -cipher aes-xts-plain64 --key-size 512 --hash sha512 --timeout 5000 --use-urandom luksFormat /dev/xxx1
    ```

    **Cryptsetup** will ask for a passphrase. Choose one that is both secure and memorable. If you forget it, **your data will be lost**.
    That will probably take a few seconds to complete, but when it’s done, it will have successfully converted your partition into an encrypted LUKS volume.


## Step 5: Open the partition
Next, you have to open the volume onto the device mapper. This is the stage at which you will be prompted for your passphrase. You can choose the name that you want your partition mapped under. It doesn’t really matter what it is, so just pick something that will be easy to remember and use.

Now your partition has a LUKS partition behind a password however it isn’t visible just yet.

To access the encrypted Luks drive, execute the following:
```shell
sudo cryptsetup open /dev/xxx1 map_point
```
Here, you can replace `map_point` with any name that you like and the partition will be mapped to.
Then use `lsblk`, now as we can see, lsblk shows the encrypted Luks partition (map_point).


## Step 6: Create a filesystem in Luks partition and mount it.
Now to store files in the encrypted partition, we need a filesystem.

I will be creating an `ext4` filesystem with label in the partition using the following command.
```shell
sudo mkfs.ext4 -L volume_label /dev/mapper/map_point
ls -l /dev/mapper/map_point
```

View, status reports the status for the mapping.
```shell
sudo cryptsetup --verbose status /dev/mapper/map_point
```

You can choose any other filesystem. For `exFAT`, use `mkfs.exfat` and for `FAT32`, use `mkfs.vfat`, and so on.

Now that we have a filesystem, we can mount it to a location to access the contents.
```shell
mkdir /mnt/luks_mount
sudo mount /dev/mapper/map_point /mnt/luks_mount
```
We are done with the implementation. Let’s see what do you need to do every time you need to access the data.


## Step 7: Unmounting and closing
Unmounting the partition is the same as a normal one, but you have to close the mapped device too.
```shell
umount /mnt/luks_mount
sudo cryptsetup close map_point
```


## Re-Open the LUKS device and Mount :
Open LUKS device to the folder.
```shell
sudo cryptsetup open /dev/xxx1 map_point
```

Mount the mapper to the directory.
```shell
mkdir /mnt/luks_mount
sudo mount /dev/mapper/map_point /mnt/luks_mount
```

## Conclusion
We have learned how to encrypt your partition and protect your sensitive data from the hands of unwanted users using Linux Unified Key Setup.
As with everything, it’s important to make sure to have a strong passphrase, have upper case characters, lower case characters, numbers, and special characters.
To read more about LUKS, head over to [Redhat forums.](https://access.redhat.com/solutions/100463) Keep exploring!

There’s plenty more, but when talking about security and encryption, things run rather deep. This guide provides the basis for encrypting and using encrypted partitions,
which is an important first step that shouldn’t be discounted.
There will definitely be more coming in this area, so be sure to check back, if you’re interested in going a bit deeper.
