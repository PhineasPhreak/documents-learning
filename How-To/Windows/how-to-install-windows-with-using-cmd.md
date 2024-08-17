# Installing Windows using CMD/DISM. (UEFI and BIOS Supported)
Reference :
- [Guide created by Andrew Lee](https://gist.github.com/Alee14/e8ce6306a038902df6e7a6d667544ac9)
- [pratyakshm - Github](https://gist.github.com/pratyakshm/f19c106205f9327e9f1d538fb91fce65)
- [Apply Windows Image using DISM Instead of Clean Install](https://www.tenforums.com/tutorials/84331-apply-windows-image-using-dism-instead-clean-install.html)

Reference Microsoft :
- [Capture and apply Windows, system, and recovery partitions](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/capture-and-apply-windows-system-and-recovery-partitions?view=windows-11)

> [!NOTE]
> Guide to install Windows 11 on any PC (does not involve ISO modifications) This guide will take you through a clean and simple way to install Windows 11 on your device by bypassing all requirements without doing any ISO modifications. Note: Guide shows fresh installation only.
>
> Be cautions when doing this when dualbooting, please backup any existing data or you will lose them all.

## Requirements
1. [Windows 11 ISO file](https://www.microsoft.com/en-ca/software-download/windows11) | [Windows 10 ISO File](https://www.microsoft.com/en-us/software-download/windows10ISO) from Microsoft.com
2. **Rufus** [Microsoft Store](https://www.microsoft.com/store/productId/9PC3H3V7Q9CH) / [GitHub](https://github.com/pbatard/rufus) / [Website](http://rufus.ie/) | **Win32 Disk Imager** [Website](https://win32diskimager.org/) / [SourceForge](https://sourceforge.net/projects/win32diskimager/)
3. USB drive [8GB or more]

## Installation
### Step 1: Use **Rufus** or **Win32Imager** to flash your USB drive with the ISO.
- Make sure that in Rufus, partition scheme is set to GPT. Everything else can be left on the default selection.

### Step 2: Boot the USB drive (skip this part if you already know how to do it)
- Reboot your PC to firmware (**Settings** -> **Update & Security** -> **Recovery** -> **Advanced startup**)
- When Windows Recovery is loaded, look around for the option "UEFI Firmware" and go to that. This will restart your device to its BIOS settings.
- Look for "Boot" section (case on most firmwares) and boot to your USB drive.

### Step 3: Open CMD
First open CMD by pressing the following keys after booting into setup: `Shift + F10`

### Step 4: Booting and manually setting up partitions for **BIOS/MBR**
On **BIOS** based machine with **MBR** disk setup will now create the required **System Reserved** partition (500 MB) using the rest of allocated space for Windows partition.

```console
diskpart
list disk
select disk (number for main disk)
clean # Clearing the partitions
convert mbr
-----------------------
(Creating recovery is optional)
create part primary size=500
format quick label="Recovery"
assign letter R
set id 27
-----------------------
create part primary
format quick label="Windows" (or label of your choice)
assign letter C (or E)
active
exit
```

### Step 4: Booting and manually setting up partitions for **UEFI/GPT**
On **UEFI** based machine with **GPT** disk the Recovery partition (450 MB), **EFI System** partition (99 MB) and **Microsoft Reserved partition** (MSR, 16 MB) will be created, rest of the allocated space being used for Windows partition.

- Use `diskpart` to open up [Microsoft DiskPart](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/diskpart) (needed for partitioning).
- Use `list disk` to list disks online on your device.
  Choose the disk you'll install Windows on. For the entirety of this tutorial, I will assume its disk 0.
  Important note: The disk must be GPT. To determine this, look for the contents of the Gpt column that is shown when list disk is used. If it contains an asterisk, the disk supports GPT.
  If you don't see an asterisk, you need to convert your disk from MBR (current) to GPT.
  To do so, select the disk using `select disk 0` and use `conv gpt`. This shall convert your disk to GPT.
- Use `select disk 0` to select disk 0 for operations.
- Use `list partition` to list all partitions in the current disk (we will assume disk has no partitions, hence create from scratch)
- Use `create partition efi size=500` to create an EFI partition of 500MB.
- Use `format fs=fat32 quick label="Windows 11"` to quick format the EFI partition with FAT32 filesystem and label "Windows 11".
- Use `assign letter a` to assign your EFI partition A:.
- Use `create partition primary` to create your primary OS partition, which will use the rest of the available disk space.
- Use `format quick` to format the partition.
- Use `assign letter c` to assign your OS partition C:.

To sum it all up :

- Main OS partition is assigned C:
- EFI/Boot partition is assigned A:
- USB drive is assigned X:
(varies)

### Go to `install.wim` directory
```console
cd [letter of installation disk]:
cd sources
```
> [!IMPORTANT]
> You may have to use `/ImageFile:D:\sources\install.wim` instead, if you using a Virtual Machine (Virtualbox, VMware).

### Step 5: Query the USB drive for Windows OS editions present (SKUs)
Listing SKUs like Home, Pro, Education, Ultimate, etc.
- Use `dism /Get-ImageInfo /ImageFile:X:\sources\install.wim` to query the USB drive.
We are going to **install Windows 11 Pro**, which is **Index:6** in my case.

> [!NOTE]
> Reference Microsoft : [DISM Deployment Image Service Management Command-Line Options](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11)

### Step 6: Deploying the images onto disk (OS installation)
Copies the content from the **install.wim** file to the main disk.
- Use `dism /Apply-Image /ImageFile:X:\sources\install.wim /Index:6 /ApplyDir:C:` to copy the OS files.

### Step 7: Creating the boot file on the (BIOS-MBR)/(UEFI-GPT) partition
[BCDBoot](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/bcdboot-command-line-options-techref-di?view=windows-11) is a command-line tool used to configure the boot files on a PC or device to run the Windows operating system.

For **MBR only** :
- Use `bootsect /nt60 C: (or E:) /force /mbr` for MBR only.

For **EFI** :
- Use `bcdboot C:\Windows /s A: /f ALL` to copy the boot files.

> [!NOTE]
> Step Optional,
> For Bypassing the OOBE entirely use this [commands](https://gist.github.com/Alee14/e8ce6306a038902df6e7a6d667544ac9#bypassing-the-oobe-entirely-p2)

### Step 8: Reboot your PC
Go ahead, **close all active Windows** and **reboot** your PC.
Using the command line `C:\Windows\System32\shutdown.exe /r /fw /f /t 0` or `wpeutil reboot`

*Sees Windows 11 boot logo*

---

# How to Install Windows 11 Without a Microsoft Account
By default, you must log in with a Microsoft account in order to install Windows 11 or go through the box ([OOBE](https://learn.microsoft.com/en-us/windows-hardware/customize/desktop/customize-oobe-in-windows-11)) setup process that triggers the first time you turn on a new laptop or desktop. Though Microsoft accounts are free, there are many reasons why you would want to install Windows 11 using a local account only.

## How to Install Windows 11 Without a Microsoft Account
There's a simple trick for setting up a local account that involves issuing a command to keep Windows from requiring Internet to install / set up and then cutting off Internet at just the right time in the setup process. This works the same way whether you are doing a clean install of Windows 11 or following the OOBE process on a store-bought computer.

1. **Follow the Windows 11 install process until you get to the "choose a country" screen.**
   Now's the time to cut off the Internet. However, before you do, you need to issue a command that prevents Windows 11 from forcing you to have an Internet connection.

2. **Hit Shift + F10**. A command prompt appears.

3. **Type** `OOBE\BYPASSNRO` to disable the Internet connection requirement.
   The computer will reboot and return you to this screen.

4. **Hit Shift + F10** again and this time **Type** `ipconfig /release`. Then **hit Enter** to disable the Internet.

5. **Close the command prompt.**

6. **Continue with the installation**, choosing the region. keyboard and second keyboard option.
   A screen saying "Let's connect you to a network" appears, warning you that you need Internet.

7. **Click "I don't have Internet"** to continue.

8. **Click Continue with limited setup.**
   A new login screen appears asking "Who's going to use this device?"

9. **Enter a username** you want to use for your local account and **click Next**.

10. **Enter a password** you would like to use and **click Next**. You can also leave this field blank and have no password, but that's not recommended.

11. **Complete the rest of the install process** as you normally would.
