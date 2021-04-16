---
title: Disk Partition
hero: images/tiny.jpg
date:
menu:
    sidebar:
        name: Disk Partition
        identifier: disk-partition
        parent: basic-storage-management
        weight: 5
---
### How to

 many utilities are available to partition disks , we will be using fdisk to make partitions on disk.

```bash
 # list disks
[s0x45ker--_(+_+)_--SysAdmin ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    0 21.3G  0 disk 
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0 20.3G  0 part 
  ├─cl-root 253:0    0 18.3G  0 lvm  /
  └─cl-swap 253:1    0  2.1G  0 lvm  [SWAP]
sdb           8:16   0    2G  0 disk 
sr0          11:0    1 1024M  0 rom  
sr1          11:1    1 1024M  0 rom
```

 we will be using **/dev/sdb** in this tutorial

```bash
# using fdisk

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x5b1cda85.

# type m to list options

Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table
```

### Create Partitions

create 3 new primary partitions

```bash
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-4194303, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-4194303, default 4194303): +256M

Created a new partition 1 of type 'Linux' and of size 256 MiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 
First sector (526336-4194303, default 526336): 
Last sector, +sectors or +size{K,M,G,T,P} (526336-4194303, default 4194303): +256M

Created a new partition 2 of type 'Linux' and of size 256 MiB.

Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 2 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (3,4, default 3): 
First sector (1050624-4194303, default 1050624): 
Last sector, +sectors or +size{K,M,G,T,P} (1050624-4194303, default 4194303): 

Created a new partition 3 of type 'Linux' and of size 1.5 GiB.
```

### List partition

```bash
Command (m for help): p
Disk /dev/sdb: 2 GiB, 2147483648 bytes, 4194304 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x5b1cda85

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048  526335  524288  256M 83 Linux
/dev/sdb2        526336 1050623  524288  256M 83 Linux
/dev/sdb3       1050624 4194303 3143680  1.5G 83 Linux
```

### Write to disk

confirm the settings by writting to disk

```bash
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

### Review changes

lets check our changes

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ lsblk | grep sdb*
sda           8:0    0 21.3G  0 disk 
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0 20.3G  0 part 
sdb           8:16   0    2G  0 disk 
├─sdb1        8:17   0  256M  0 part 
├─sdb2        8:18   0  256M  0 part 
└─sdb3        8:19   0  1.5G  0 part

```

### Place a Filesystem on Disk

 lets put filesystem on our newly created partitions

```bash
s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mkfs.ext4 /dev/sdb1 
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 262144 1k blocks and 65536 inodes
Filesystem UUID: 68ea7cfb-7d8d-4fb9-a40d-6c16ff282367
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729, 204801, 221185

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done 

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mkfs.ext4 /dev/sdb2 
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 262144 1k blocks and 65536 inodes
Filesystem UUID: 0159b426-cea0-40c0-85d4-670be5da567a
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729, 204801, 221185

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done 

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mkfs.ext4 /dev/sdb3
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 392960 4k blocks and 98304 inodes
Filesystem UUID: 6fbbcc36-35bf-4959-b60f-b041b3eb09d6
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: don
```

### Creating Mount Points

# lets create mount points

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ mkdir mnt1 mnt2 mnt3
```

### Mount partition

 lets mount them

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount /dev/sdb1 mnt1
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount /dev/sdb2 mnt2
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount /dev/sdb3 mnt3
```

### Check Mount

 lets check whether they were mounted 

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ df -Th | grep "mnt*"
/dev/sdb1           ext4      240M  2.1M  222M   1% /home/s0x45ker/mnt1
/dev/sdb2           ext4      240M  2.1M  222M   1% /home/s0x45ker/mnt2
/dev/sdb3           ext4      1.5G  4.5M  1.4G   1% /home/s0x45ker/mnt3
```

### Unmount partition

unmount after using them

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo umount mnt1 mnt2 mnt3 
```

### Delete mount dir

 delete the mount points

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ rmdir mnt*
```

### Rechcek

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ df -Th | grep "mnt*"
[s0x45ker--_(+_+)_--SysAdmin ~]$
```
