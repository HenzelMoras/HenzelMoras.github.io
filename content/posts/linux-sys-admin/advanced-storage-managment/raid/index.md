---
title: RAID
date:
hero: images/gun.jpg
menu:
    sidebar:
        name: RAID
        identifier: raid
        parent: advanced-storage-management
        weight: 2

---
### About RAID

Three essential features of RAID are:

- mirroring: writing the same data to more than one disk
- striping: splitting of data to more than one disk
- parity: extra data is stored to allow problem detection and repair, yielding fault tolerance.

Thus, use of RAID can improve both performance and reliability.

**mdadm** is used to create and manage RAID devices.

Once created, the array name, **/dev/mdX**, can be used just like any other device, such as **/dev/sdb1**.

### RAID Levels

<details>
<summary> RAID 0 </summary>
<p>RAID 0 uses only striping. Data is spread across multiple disks. However, in spite of the name, there is no redundancy and there is no stability or recovery capabilities. In fact, if any disk fails, data will be lost. But performance can be improved significantly because of parallelization of I/O tasks.</p>
</details>

<details>
<summary> RAID 1</summary>
<p>RAID 1 uses only mirroring; each disk has a duplicate. This is good for recovery. At least two disks are required.</p>
</details>

<details>
<summary> RAID 5</summary>
<p>RAID 1 uses only mirroring; each disk has a duplicate. This is good for recovery. At least two disks are required.</p>
</details>

<details>
<summary> RAID 6</summary>
<p>RAID 6 has striped disks with dual parity; it can handle loss of two disks, and requires at least 4 disks. Because RAID 5 can impose significant stress on disks, which can lead to failures during recovery procedures, RAID 6 has become more important.</p>
</details>

<details>
<summary> RAID 10</summary>
<p> RAID 10 is a mirrored and striped data set. At least 4 drives are needed. </p>
</details>

As a general rule, adding more disks improves performance.

### Creating RAID Partitions

we will make use of the lvm partition we created last tutorial or if u want use any other disk drives

last tutorial we only created one lvm lets create one more since RAID 1 requires two disk drives

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo lvcreate -L 200M -n lvmp2 myvgName
  Logical volume "lvmp2" created.

[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo lvcreate -L 200M -n lvmp3 myvgName
  Logical volume "lvmp3" created.
```

lets list them for better view

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo lvdisplay | grep lvm
  LV Path                /dev/myvgName/mylvmName
  LV Name                mylvmName
  LV Path                /dev/myvgName/lvmp2
  LV Name                lvmp2
```

lets create using mdadm make sure  to umount before creating

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mdadm -C /dev/md0 --level=1 --raid-disks=2 /dev/myvgName/mylvmName /dev/myvgName/lvmp2 
mdadm: /dev/myvgName/mylvmName appears to contain an ext2fs file system
       size=716800K  mtime=Sat Apr 17 14:54:37 2021
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
mdadm: largest drive (/dev/myvgName/mylvmName) exceeds size (203776K) by more than 1%
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```

### Filesystem on Disk /dev/md0

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mkfs.ext4 /dev/md0 
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 203776 1k blocks and 51000 inodes
Filesystem UUID: a10242e4-ce80-44d7-afea-cce0ab0a1c0a
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information:  0/done
```

### Mount RAID

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mount /dev/md0 /mnt/
```

within /etc/fstab

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo nano /etc/fstab
GNU nano 2.9.8                                                                                       /etc/fstab                                                                                                  

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

/dev/myvgName/mylvmName /mylvmpnt               ext4    defaults        0 0
/dev/md0                /mnt                    ext4    defaults        0 0
```

### RAID config in

/etc/mdadm.conf

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"
```

### Monitor RAID Status

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ cat /proc/mdstat 
Personalities : [raid1] 
md0 : active raid1 dm-3[1] dm-2[0]
      203776 blocks super 1.2 [2/2] [UU]
      
unused devices: <none>
```

### RAID Hot Spares

lets remove are fake faulty drive lvm2 

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mdadm --remove /dev/md0 /dev/myvgName/lvmp2 
mdadm: hot removed /dev/myvgName/lvmp2 from /dev/md0
```

add a new one

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo mdadm --add /dev/md0 /dev/myvgName/lvmp3 
mdadm: added /dev/myvgName/lvmp3
```

lets check spare

```bash
[s0x45ker--_(+_+)_--SysAdmin ~]$ lsblk 
NAME                   MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                      8:0    0 21.3G  0 disk  
├─sda1                   8:1    0    1G  0 part  /boot
└─sda2                   8:2    0 20.3G  0 part  
  ├─cl-root            253:0    0 18.3G  0 lvm   /
  └─cl-swap            253:1    0  2.1G  0 lvm   [SWAP]
sdb                      8:16   0    2G  0 disk  
├─sdb1                   8:17   0  256M  0 part  
│ └─myvgName-mylvmName 253:2    0  700M  0 lvm   
│   └─md0                9:0    0  199M  0 raid1 
├─sdb2                   8:18   0  256M  0 part  
│ └─myvgName-mylvmName 253:2    0  700M  0 lvm   
│   └─md0                9:0    0  199M  0 raid1 
└─sdb3                   8:19   0  1.5G  0 part  
  ├─myvgName-mylvmName 253:2    0  700M  0 lvm   
  │ └─md0                9:0    0  199M  0 raid1 
  ├─myvgName-lvmp2     253:3    0  200M  0 lvm   
  └─myvgName-lvmp3     253:4    0  200M  0 lvm   
    └─md0                9:0    0  199M  0 raid1
```

### RAID Monitoring

we can also use the **mdmonitor** service by editing **/etc/mdadm.conf** and adding a line like:

**MAILADDR admin@gmail.com**

so that it notifies you with email sent to **admin@gmail.com** when a problem occurs with a RAID device, such as when any of the arrays fail to start or fall into a degraded state. You turn it on with:

**$ sudo systemctl start mdmonitor**

and make sure it starts at boot with:

**$ sudo systemctl enable mdmonitor**

## 💡

On Ubuntu systems, the service is called **mdadm** rather than **mdmonitor**.