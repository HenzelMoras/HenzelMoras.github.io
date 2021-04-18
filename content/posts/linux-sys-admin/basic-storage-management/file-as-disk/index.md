---
title: File As Disk
date: 2021-04-17
hero: images/juice.jpg
menu:
    sidebar:
        name: File As Disk
        identifier: file-as-disk
        parent: basic-storage-management
        weight: 7
---
### About

 we can create Diskpartition using a file as image 
 we can achieve this using 'dd' or 'losetup'

### Using: 'dd'

create a file full of zeros using dd

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ dd if=/dev/zero of=imagefile bs=1M count=1024 #create a file full of zeros
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 0.904085 s, 1.2 GB/s
```

### Format Filesystem Type

next we must put a filesystem on it
`Note u can create different filesystem formats 'mkfs.<type>`

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ mkfs.ext4 imagefile 
mke2fs 1.45.6 (20-Mar-2020)
Discarding device blocks: done                            
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: 3ba526cc-875d-4315-ab6a-534fe7ac58ff
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
```

### Create Mount Point

Now lets mount the filesystem, firstly by creating a directory as mointpoint

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ mkdir mntpoint
# mount the filesystem

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount imagefile mntpoint/

# list the mount point

[s0x45ker--_(+_+)_--SysAdmin ~]$ df -h | grep mntpoint
/dev/loop0           976M  2.6M  907M   1% /home/s0x45ker/mntpoint

# unmount the filesystem

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo umount mntpoint/

# returns nothing

[s0x45ker--_(+_+)_--SysAdmin ~]$ df -h | grep mntpoint
[s0x45ker--_(+_+)_--SysAdmin ~]$

# delete the directory 

[s0x45ker--_(+_+)_--SysAdmin ~]$ rmdir mntpoint
[s0x45ker--_(+_+)_--SysAdmin ~]$
```

### Using: 'losetup'

check man pages of `losetup` and `parted`
once again, you can reuse imagefile or create another one using 'dd'

### Check Free Loop Device

```bash
# display free loop device

[s0x45ker--_(+_+)_--SysAdmin ~]$ losetup -f
/dev/loop0
```

### Setup Loop

```bash
# create an imagefile from it

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo losetup /dev/loop0 imagefile
```

### Create Partition

```bash
# make partition label 

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo parted -s /dev/loop0 mklabel msdos

# create 3 partitions on the disk

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo parted -s /dev/loop0 unit MB mkpart primary ext4 0 256
Warning: The resulting partition is not properly aligned for best performance: 1s % 2048s != 0s
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo parted -s /dev/loop0 unit MB mkpart primary ext4 256 512
Warning: The resulting partition is not properly aligned for best performance: 500001s % 2048s != 0s
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo parted -s /dev/loop0 unit MB mkpart primary ext4 512 1024
Warning: The resulting partition is not properly aligned for best performance: 1000001s % 2048s != 0s

```

### View Partition

```bash
# view the partitions

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo fdisk -l /dev/loop0
Disk /dev/loop0: 1 GiB, 1073741824 bytes, 2097152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xcf49ab35

Device       Boot   Start     End Sectors   Size Id Type
/dev/loop0p1            1  500000  500000 244.1M 83 Linux
/dev/loop0p2       500001 1000000  500000 244.1M 83 Linux
/dev/loop0p3      1000001 2000000 1000000 488.3M 83 Linux

[s0x45ker--_(+_+)_--SysAdmin ~]$ ls -l /dev/loop0*
brw-rw----. 1 root disk   7, 0 Apr 12 21:22 /dev/loop0
brw-rw----. 1 root disk 259, 0 Apr 12 21:22 /dev/loop0p1
brw-rw----. 1 root disk 259, 1 Apr 12 21:22 /dev/loop0p2
brw-rw----. 1 root disk 259, 2 Apr 12 21:22 /dev/loop0p3

```

### Place Filesystem

```bash
# put filesystem on partitions

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mkfs.ext4 /dev/loop0p1 
mke2fs 1.45.6 (20-Mar-2020)
Discarding device blocks: done                            
Creating filesystem with 250000 1k blocks and 62744 inodes
Filesystem UUID: 3b8ebc72-3a3f-46d3-9989-03f24184d248
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729, 204801, 221185

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done 

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mkfs.ext4 /dev/loop0p2
mke2fs 1.45.6 (20-Mar-2020)
Discarding device blocks: done                            
Creating filesystem with 250000 1k blocks and 62744 inodes
Filesystem UUID: b6f68d7d-7aac-4bd5-86de-b40807f44a74
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729, 204801, 221185

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done 

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mkfs.ext4 /dev/loop0p3
mke2fs 1.45.6 (20-Mar-2020)
Discarding device blocks: done                            
Creating filesystem with 499713 1k blocks and 125416 inodes
Filesystem UUID: 7029e10e-db19-4328-bb0b-c2162a2d2d7b
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729, 204801, 221185, 401409

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
```

### Create Mountpoint

```bash
# creating directories for mounting

[s0x45ker--_(+_+)_--SysAdmin ~]$ mkdir mnt1 mnt2 mnt3

# mount the filesystem on repective directories

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount /dev/loop0p1 mnt1
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount /dev/loop0p2 mnt2
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount /dev/loop0p3 mnt3

# check wether it exists

[s0x45ker--_(+_+)_--SysAdmin ~]$ df -Th | grep "/dev/loop0*"
/dev/loop0p1        ext4      233M  2.1M  215M   1% /home/s0x45ker/mnt1
/dev/loop0p2        ext4      233M  2.1M  215M   1% /home/s0x45ker/mnt2
/dev/loop0p3        ext4      465M  2.3M  434M   1% /home/s0x45ker/mnt3
```

### Delete and Remove

```bash
# delete and remove after use
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo umount mnt1 mnt2 mnt3
[s0x45ker--_(+_+)_--SysAdmin ~]$ rmdir mnt*
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo losetup -d /dev/loop0
```