---
title: Interacting With Disks
hero: images/force.png
menu:
    sidebar:
        name: Interacting With Disks
        identifier: interacting-with-disks
        parent: basic-storage-management
        weight: 4
---
### Mount

The mount program allows attaching at any point in the tree structure; umount allows detaching them.

The mount point is the directory where the filesystem is attached. It must exist before mount can use it; **mkdir** can be used to create an empty directory. If a pre-existing directory is used and it contains files prior to being used as a mount point, they will be hidden after mounting. These files are not deleted and will again be visible when the filesystem is unmounted.

By default, only the superuser can mount and unmount filesystems.

 Each filesystem is mounted under a specific directory, as in:

`$ sudo mount UUID=26d58ee2-9d20-4dc7-b6ab-aa87c3cfb69a /home`

  UUIDs are unique identifiers

**`$ sudo mount -t ext /dev/sdb4 /home`**

- Mounts an ext4 filesystem
- Usually not necessary to specify the type with the **t** option
- The filesystem is located on a specific partition of a hard drive (**/dev/sdb4**)
- The filesystem is mounted at the position **/home** in the current directory tree
- Any files residing in the original **/home** directory are hidden until the partition is unmounted.

mount all filesystems mentioned in `/etc/fstab` and many filesystem specific

```bash
mount -a 
```

which remounts a filesystem with a `read-only` attribute.

```bash
sudo mount -o remount,ro /fs
```

 to see more options

```bash
mount —help 
```

displays currently mounted filesystems

```bash
mount 
```

mounting network filesystems:

```bash
mount -t nfs <IP>:/NameOfShare /mnt/my_mounted_nfs
```

or in `/etc/fstab`. Put the following line in /etc/fstab to mount on boot or with `mount -a`:

```bash
myserver.com:/shdir /mnt/shdir nfs rsize=8192,wsize=8192,timeo=14,intr 0 0
```

### Umount

```bash
$ umount [device-file | mount-point]
```

Below are some examples of how to unmount a filesystem:

- Unmount the **/home** filesystem:

    ```bash
    $ sudo umount /home
    ```

- Unmount the **/dev/sda3** device:

    ```bash
    $ sudo umount /dev/sda3
    ```

Note that the command to unmount a filesystem is umount `(not unmount!).`

The most common error encountered when unmounting a filesystem is trying to do this on a filesystem currently in use; i.e., there are current applications using files or other entries in the filesystem.

This can be as simple as having a terminal window open in a directory on the mounted filesystem. Just using **`cd`** in that window, or killing it, will get rid of the**`device is busy`** error and allow unmounting.

However, if there are other processes inducing this error, you must kill them before unmounting the filesystem. You can use **`fuser`**to find out which users are using the filesystem and kill them (be careful with this, you may also want to warn users first). You can also use **`lsof** ("list open files")` to try and see which files are being used and blocking unmounting.

### **mkfs**

Every filesystem type has a utility for formatting (making) a filesystem on a partition. The generic name for these utilities is **mkfs**. However, this is just a frontend for filesystem-specific programs, each of which may have particular options.

The general format for **mkfs** is:

```bash
**mkfs [-t fstype] [options] [device-file]**
```

where **[device-file]** is usually a device name like **/dev/sda3** or **/dev/vg/lvm1**.

The following two commands are entirely equivalent:

```bash
**$ sudo mkfs -t ext4 /dev/sda10**
```

```bash
**$ sudo mkfs.ext4 /dev/sda10**
```

Each filesystem type has its own particular options that can be set when formatting. For example, when creating an ext4 filesystem, one thing to keep in mind are the journalling settings. These include defining the journal file size and whether or not to use an external journal file.

###  fstab

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Thu Nov 12 19:37:28 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
	/dev/mapper/cl-root                           /           xfs    defaults  0 0
	UUID=03f520f5-2aec-411c-a15b-3e2f62ffc37c     /boot       ext4   defaults  1 2
	/dev/mapper/cl-swap                           swap        swap   defaults  0 0
```

Each record in the **/etc/fstab** file contains white space separated files of information about a filesystem to be mounted:

- Device file `name, label, or UUID`For filesystems which do not have a device node, such as tmpfs, proc, and sysfs, this field is just a placeholder; sometimes, you will see the word *none* in that column, or used on the command line.
- `Mount point`This can also be a placeholder, like for swap, which is not mounted anywhere.
- `fs type`Filesystem type (i.e., ext4, xfs, btrfs, vfat)
- A comma-separated list of options e.g.(noauto,x-systemd.automount,x-systemd.device-timeout=10,x-systemd.idle-timeout=3)
- **`dump`** frequency (or a 0)This is used by the rarely used **`dump -w`** command.
- **`fsck`** pass number (or 0, meaning do not check state at boot).

### Fsck

Every filesystem type has a utility designed to check for errors (and hopefully fix any that are found). The generic name for these utilities is fsck. However, this is just a frontend for filesystem-specific programs.

```bash
fsck [-t fstype] [options] [device-file]
```

You can control whether any errors found should be fixed one by one manually with the **-r** option, or automatically, as best possible, by using the **-a** option, etc. In addition, each filesystem type may have its own particular options that can be set when checking.

Note that journalling filesystems are much faster to check than older generation filesystems for two reasons:

- You rarely need to scan the entire partition for errors, as everything but the very last transaction has been logged and confirmed, so it takes almost no time to check.
- Even if you do check the whole filesystem, newer filesystems have been designed with fast fsck in mind; older filesystems did not think much about this when they were designed as sizes were much smaller.

```bash
$ sudo fsck -t ext4 /dev/sda10
				or
$ sudo fsck.ext4 /dev/sda10
```

if the filesystem is of a type understood by the operating system, you can almost just do:

```bash
sudo fsck /dev/sda10
```

and the system will figure out the type by examining the first few bytes on the partition.

fsck is run automatically after a set number of mounts or a set interval since the last time it was run or after an abnormal shutdown. It should only be run on unmounted filesystems. You can force a check of all mounted filesystems at boot by doing:

```bash
$ sudo touch /forcefsck

$ sudo reboot
```

The file /forcefsck will disappear after the successful check. One reason this is a valuable trick is it can do a fsck on the root filesystem, which is hard to do on a running system.

To list block devices and their attributes

### **blkid**

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo blkid /dev/sd*
/dev/sda: PTUUID="efac2304" PTTYPE="dos"
/dev/sda1: UUID="03f520f5-2aec-411c-a15b-3e2f62ffc37c" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="efac2304-01"
/dev/sda2: UUID="HkaGdc-0SM4-q58B-GLpF-yBKh-dluM-Zb7psU" TYPE="LVM2_member" PARTUUID="efac2304-02"
/dev/sdb: PTUUID="5b1cda85" PTTYPE="dos"
/dev/sdb1: UUID="68ea7cfb-7d8d-4fb9-a40d-6c16ff282367" BLOCK_SIZE="1024" TYPE="ext4" PARTUUID="5b1cda85-01"
/dev/sdb2: UUID="0159b426-cea0-40c0-85d4-670be5da567a" BLOCK_SIZE="1024" TYPE="ext4" PARTUUID="5b1cda85-02"
/dev/sdb3: UUID="6fbbcc36-35bf-4959-b60f-b041b3eb09d6" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="5b1cda85-03"
```

### **lsblk**

A related utility is lsblk which presents block device information in a tree format, as in the following screenshot.

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    0 21.3G  0 disk 
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0 20.3G  0 part 
  ├─cl-root 253:0    0 18.3G  0 lvm  /
  └─cl-swap 253:1    0  2.1G  0 lvm  [SWAP]
sdb           8:16   0    2G  0 disk 
├─sdb1        8:17   0  256M  0 part 
├─sdb2        8:18   0  256M  0 part 
└─sdb3        8:19   0  1.5G  0 part 
sr0          11:0    1 1024M  0 rom  
sr1          11:1    1 1024M  0 rom
```