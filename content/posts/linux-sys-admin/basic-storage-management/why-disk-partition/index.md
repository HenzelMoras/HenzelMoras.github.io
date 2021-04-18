---
title: Why Disk Partition ?
date: 2021-04-17
menu:
    sidebar:
        name: Why Disk Partition ?
        identifier: why-disk-partition
        parent: basic-storage-management
        weight: 1
---
### Why Partition?
There are multiple reasons as to why it makes sense to divide your system data into multiple partitions, including:

- Separation of user and application data from operating system files
- Sharing between operating systems and/or machines
- Security enhancement by imposing different quotas and permissions for different system parts
- Size concerns; keeping variable and volatile storage isolated from stable
- Performance enhancement of putting most frequently used data on faster storage media
- Swap space can be isolated from data and also used for hibernation storage.

Deciding what to partition and how to separate your partitions is cause for thought. The reasons to have distinct partitions include increased granularity of  security, quota, settings or size restrictions. You could have distinct partitions to allow for data protection.

A common partition layout contains a boot partition, a partition for the root filesystem /, a swap partition, and a partition for the /home directory tree.

Keep in mind that it is more difficult to resize a partition after the fact than during install/creation time. Plan accordingly.

