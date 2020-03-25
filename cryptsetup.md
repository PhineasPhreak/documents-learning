# cryptsetup *(version : 2.0.2)*
* You can also use old <action> syntax aliases :
    * open: create (plainOpen), luksOpen, loopaesOpen, tcryptOpen
    * close: remove (plainClose), luksClose, loopaesClose, tcryptClose

## Create, Mount and Umount **cryptsetup** device :
List device on the system
```shell
$ ls -l /dev/xvdc
```

---
Creation LUKS format device with verify passphrase
```shell
$ cryptsetup -y -v LukFormat /dev/xvdc
```

---
Open LUKS device to the folder (backup2). </br>
```shell
$ cryptsetup LuksOpen /dev/xvdc backup2
```

---
List device mapper on the system
```shell
$ ls -l /dev/mapper/backup2
```

---
Format your device with your label in the mapper device
```shell
$ mkfs.ext4 -L volume_label /dev/mapper/backup2 # (DO NOT FORGET THE LABEL)
```
---
Create a directory for mounting device
```shell
$ mkdir /backup2
```

---
Mount the mapper to my new directory
```shell
$ mount /dev/mapper/backup2 /backup2
```

---
View, mounting situation with size human readable
```shell
$ df -H
```

---
Got to 'backup2' directory for view the contents
```shell
$ cd /backup2 
$ ls -l
```

---
View, status for the mapper with verbose option
```shell
$ cryptsetup -v status /dev/mapper/backup2
```

---
Umount 'backup2'
```shell
$ umount /backup2
```

---
Close the LUKS
```shell
$ cryptsetup LuksClose /dev/mapper/backup2
```

## Re-Open the LUKS device and Mount :
Open LUKS device to the folder (backup2). </br>
```shell
$ cryptsetup LuksOpen /dev/xvdc backup2
```

---
Mount the mapper to the directory 'backup2'
```shell
$ mount /dev/mapper/backup2 /backup2/
```

---
View, the situation
```shell
$ df -H
```
