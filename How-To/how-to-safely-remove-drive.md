# Command Line Equivalent of Safely Remove Drive
The `udisks` command is most likely what you are looking for.

While `sudo unmount /dev/sdXY` will work, udisks can do this without root level (sudo) permissions.

If you have a drive `/dev/sdXY`, mounted, where X is a letter representing your usb disk and Y is the partition number (usually 1), you can use the following commands to safely remove the drive:
```bash
udisks --unmount /dev/sdXY
udisks --detach /dev/sdX
```
For a practical example, if I have the partition /dev/sdb1 mounted, I would run this to unmount and detach it:
```bash
udisks --unmount /dev/sdb1
udisks --detach /dev/sdb
```
I originally found this through this question: [superuser.com](https://superuser.com/a/430470/176493).

## Using udisks2 :
In the newer ubuntu distributions (I'm unsure of when the switch occurred), udisks2 is installed instead of udisks.

Mirroring the commands above, to unmount and detach a disk with udisks2:
```bash
udisksctl unmount -b /dev/sdXY
udisksctl power-off -b /dev/sdX
```
Example if my drive is `/dev/sdb1`:
```bash
udisksctl unmount -b /dev/sdb1
udisksctl power-off -b /dev/sdb
```