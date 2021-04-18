---
title: Quotas
hero: images/work.jpg
date: 2021-04-18
menu:
    sidebar:
        name: Quotas
        identifier: quotas
        parent: advanced-storage-management
        weight: 4
---
### **Filesystem Quotas**

Linux can use and enforce quotas on filesystems. Disk quotas allow administrators to control the maximum space particular users (or groups) are allowed. Considerable flexibility is allowed and quotas can be assigned on a per filesystem basis. Protection is provided against a subset of users exhausting collective resources.

### Utilities

<details> 
<summary>quotacheck </summary>
<p>quotacheck generates and updates quota accounting files.</p>
</details>


<details> 
<summary>quotaon </summary>
<p>quotaon enables quota accounting.</p>
</details>


<details> 
<summary>quotaoff </summary>
<p>quotaoff disables quota accounting.</p>
</details>


<details> 
<summary>edquota </summary>
<p>edquota used for editing user or group quotas.</p>
</details>


<details> 
<summary>quota </summary>
<p>quota reports on usage and limits.</p>
</details>

Quota operations require the existence of the files **aquota.user** and **aquota.group** in the root directory of the filesystem using quotas.

Quotas may be enabled or disabled on a per-filesystem basis. In addition, Linux supports the use of quotas based on user and group IDs.

Different filesystem types may have additional quota-related utilities, such as **xfs_quota**.

### Steps

1. Mount the filesystem with user and/or group quota options:Add the **usrquota** and/or **grpquota** options to the filesystems entry in **/etc/fstab**Remount the filesystem (or mount it if new)
2. Run **quotacheck** on the filesystem to set up quotas
3. Enable quotas on the filesystem
4. Set quotas with the **edquota** program.

### 1. Userquota in Fstab

in this example we will use a file as disk and add quota settings within /etc/fstab 

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ dd if=/dev/zero of=imagefile bs=1M count=1024
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 1.0488 s, 1.0 GB/s

[s0x45ekr--_(+_+)_--Sysadmin ~]$ mkfs.ext4 imagefile 
mke2fs 1.45.6 (20-Mar-2020)
Discarding device blocks: done                            
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: e9b2993f-b882-404b-8692-d411c8e1adb6
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo nano /etc/fstab                                                                                                                                                                                  
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
/dev/mapper/cl-root     /                       xfs     defaults        0 0
UUID=03f520f5-2aec-411c-a15b-3e2f62ffc37c /boot ext4    defaults        1 2
/dev/mapper/cl-swap     swap                    swap    defaults        0 0

/home/s0x45ker/imagefile /mnt                   ext4    loop,usrquota   1 2
```

finally mount em

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo mount -a
[s0x45ekr--_(+_+)_--Sysadmin ~]$ df -Th | grep mnt
/dev/loop0          ext4      976M  2.6M  907M   1% /mnt
```

### 2. Setup quota

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo quotacheck -u /mnt/
```

### 3. Enable quota

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo quotaon -u /mnt/
```

might need to change ownership so that we can exhust quotas as that user

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo quotaon -u /mnt/

[s0x45ekr--_(+_+)_--Sysadmin ~]$ ls -al / | grep mnt
drwxr-xr-x.   3 s0x45ker s0x45ker 4096 Apr 18 16:33 mnt
```

### 4.  Set Quota

 `soft limit : 200` which can be exceeded but will show a warning

`hard limt : 600` which will stop the operation if size exceeds

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo edquota -u s0x45ker
Disk quotas for user s0x45ker (uid 1001):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/loop0                      600        200        600          4        0        0
```

ok so now lets put our user `s0x45ker` quotas to the test

```bash
[s0x45ekr--_(+_+)_--Sysadmin /mnt]$ dd if=/dev/zero of=file bs=1024 count=200
loop0: warning, user block quota exceeded.
200+0 records in
200+0 records out
204800 bytes (205 kB, 200 KiB) copied, 0.00263448 s, 77.7 MB/s

[s0x45ekr--_(+_+)_--Sysadmin /mnt]$ sudo quota s0x45ker
Disk quotas for user s0x45ker (uid 1001): 
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
     /dev/loop0     204*    200     600   6days       2       0       0        

[s0x45ekr--_(+_+)_--Sysadmin /mnt]$ dd if=/dev/zero of=file1 bs=1024 count=200
200+0 records in
200+0 records out
204800 bytes (205 kB, 200 KiB) copied, 0.00249903 s, 82.0 MB/s
[s0x45ekr--_(+_+)_--Sysadmin /mnt]$ sudo quota s0x45ker
Disk quotas for user s0x45ker (uid 1001): 
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
     /dev/loop0     404*    200     600   6days       3       0       0        

[s0x45ekr--_(+_+)_--Sysadmin /mnt]$ dd if=/dev/zero of=file2 bs=1024 count=200
loop0: write failed, user block limit reached.
dd: error writing 'file2': Disk quota exceeded
197+0 records in
196+0 records out
200704 bytes (201 kB, 196 KiB) copied, 0.000840477 s, 239 MB/s
```

as u can see the file operatition stops when it exceeds hard limit