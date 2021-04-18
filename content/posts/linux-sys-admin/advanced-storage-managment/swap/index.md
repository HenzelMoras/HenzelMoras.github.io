---
title: swap
hero: images/thugh.png
date: 2021-04-18
menu:
    sidebar:
        name: swap
        identifier: swap
        parent: advanced-storage-management
        weight: 3
---
### About Swap

Linux employs a virtual memory system, in which the operating system can function as if it had more memory than it really does. This kind of memory overcommission functions in two ways:

- Many programs do not actually use all the memory they are given permission to use. Sometimes, this is because child processes inherit a copy of the parent's memory regions utilizing a COW (Copy On Write) technique, in which the child only obtains a unique copy (on a page-by-page basis) when there is a change.
- When memory pressure becomes important, less active memory regions may be swapped out to disk, to be recalled only when needed again.

Such swapping is usually done to one or more dedicated partitions or files; Linux permits multiple swap areas, so the needs can be adjusted dynamically. Each area has a priority, and lower priority areas are not used until higher priority areas are filled.

In most situations, the recommended swap size is the total RAM on the system. You can see what your system is currently using for swap areas by looking at **/proc/swaps** and getting basic memory statistics with **free**:

`cat /proc/swaps`

`free -m`

The only commands involving swap are:

- **mkswap**: format a swap partition or file
- **swapon**: activate a swap partition or file
- **swapoff**: deactivate a swap partition or file.

At any given time, most memory is in use for caching file contents to prevent actually going to the disk any more than necessary, or in a sub-optimal order or timing. Such pages of memory are never swapped out as the backing store is the files themselves, so writing out a swap would be pointless; instead, dirty pages (memory containing updated file contents that no longer reflect the stored data) are flushed out to disk.

It is also worth pointing out that in Linux memory used by the kernel itself, as opposed to application memory, is *never swapped out*, in distinction to some other operating systems.

### Managing Swap

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ cat /proc/swaps 
Filename				Type		Size	Used	Priority
/dev/dm-1                               partition	2166780	93184	-2
```

### File As Swap(`mkswap`)

we can also use disk partitions as swap 

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ dd if=/dev/zero of=swapfile bs=1M count=1024
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 1.05868 s, 1.0 GB/s

[s0x45ekr--_(+_+)_--Sysadmin ~]$ ls | grep swapfile
swapfile 
```

`file permissions : read,write` 

`user and group : root`

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo chown root:root swapfile
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo chmod 600 swapfile
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo mkswap swapfile
mkswap: imagefile: warning: wiping old swap signature.
Setting up swapspace version 1, size = 1024 MiB (1073737728 bytes)
no label, UUID=e96df880-5122-45bf-853e-d7c321818e04
```

### Turn on Swap

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo swapon swapfile
```

check if it exists

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ cat /proc/swaps 
Filename				Type		Size	UsedPriority
/dev/dm-1                               partition	2166780	157184	-2
/home/s0x45ker/swapfile                file		1048572	0	-3
```

### Turn off Swap

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo swapoff swapfile

[s0x45ekr--_(+_+)_--Sysadmin ~]$ cat /proc/swaps 
Filename				Type		Size	UsedPriority
/dev/dm-1                               partition	2166780	156852	-2
```

finally delete the imagefile after use

```bash
[s0x45ekr--_(+_+)_--Sysadmin ~]$ sudo rm swapfile

[s0x45ekr--_(+_+)_--Sysadmin ~]$ ls swapfile
ls: cannot access 'imagfile': No such file or directory
```