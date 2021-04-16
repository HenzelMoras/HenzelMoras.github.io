---
title: Backup Partition
hero: images/me.jpg
menu:
    sidebar:
        name: Backup Partition
        identifier: backup-partition
        parent: basic-storage-management
        weight: 6
---
### **Using dd for backup**

we can use `dd` or `gfdisk`  

The `dd` program is very useful for making copies of raw disk space. A
  common joke with `dd' is that is stands for *data destroyer*, so it
  should be noted that it's a very dangerous utility.

  we will use one of the partition we created from earlier posts /dev/sdb1 

 `**lets create a file within disk than backup the disk and delete the file and restore the disk**`

### Mount The Disk

firstly, lets mount the disk 

```bash
[s0x45ker--_(+_+)_--SysAdmin /]$ sudo mount /dev/sdb1 mnt/
```

### Check Mount

lets check the mount

```bash
[s0x45ker--_(+_+)_--SysAdmin /]$ df -Th | grep sdb1
/dev/sdb1           ext4      240M  2.1M  222M   1% /mnt

										or

[s0x45ker--_(+_+)_--SysAdmin /]$ lsblk | grep sdb1
├─sdb1        8:17   0  256M  0 part /mnt
```

### Create file to backup

lets create a file within the mount point

```bash
[s0x45ker--_(+_+)_--SysAdmin /mnt]$ sudo dd if=/dev/zero of=imagefile bs=1M count=230
230+0 records in
230+0 records out
241172480 bytes (241 MB, 230 MiB) copied, 0.247848 s, 973 MB/s

[s0x45ker--_(+_+)_--SysAdmin /mnt]$ ls
imagefile  lost+found
```

### Disk backup

create backup of the disk 

```bash
[s0x45ker--_(+_+)_--SysAdmin /]$ sudo dd if=/dev/sdb1 of=backup.sdb1
524288+0 records in
524288+0 records out
268435456 bytes (268 MB, 256 MiB) copied, 1.004 s, 267 MB/s
```

### Delete File After Disk Backup

lets delete the file

```bash
s0x45ker--_(+_+)_--SysAdmin /]$ sudo rm imagefile

[s0x45ker--_(+_+)_--SysAdmin /mnt]$ ls   # imagefile no longer exists
lost+found  
```

### Restore Disk Backup

lets retsore the backup

firstly unmount the disk or it might say `imagefile: cannot open `imagefile' (No such file or directory)` when restored

```bash
[s0x45ker--_(+_+)_--SysAdmin /]$ cd .. ; sudo umount /mnt && ls
backup.sdb1  bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

restore backup with following command

```bash
[s0x45ker--_(+_+)_--SysAdmin /]$ sudo dd if=backup.sdb1 of=/dev/sdb1
524288+0 records in
524288+0 records out
268435456 bytes (268 MB, 256 MiB) copied, 11.4738 s, 23.4 MB/s
```

### View Backup File

the file hase been restored

```bash
[s0x45ker--_(+_+)_--SysAdmin /]$ sudo mount /dev/sdb1 mnt/ && cd /mnt/ ; ls
imagefile  lost+found
```

we have successfully backedup and restored  a disk