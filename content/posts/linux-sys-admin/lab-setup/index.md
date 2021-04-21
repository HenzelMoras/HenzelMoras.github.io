---
title: Lab Setup
date: 2021-04-21
hero: images/lab.jpg
menu:
    sidebar:
        name: Lab Setup
        identifier: lab-setup
        parent: linux-sys-admin
        weight: 1
---
This Post we will focus on building our lab to practice Sys-Admin Tasks.

There are multiple ways of setting up the lab with different distro's.
 
* Cloud (AWS,GCP etc) Free tier.
* Hypervisor (virtual box, Vmware etc)
* Vagrant (which would use an hypervisor but convenient for ready to use boxs)

many more.


 I will choose Hypervisor(Virtual Box) with my host OS as Kubuntu.

 ### Download VirtualBox

<a id="virtualbox" href="https://www.virtualbox.org/wiki/Downloads" target="_blank">virtualbox-link</a> 

![virtualbox-download](images/virtualbox-download.png)

select the distribution of your choice and download it. 



### Install VirtualBox

installation instructions are provided within the docs.

in my case 

![install-vb](images/install-vb.png)


### Download Extension Pack

Download the extension from the <a href=#virtualbox>`virtualbox-link`</a>

![extention-pack](images/extension-pack.png)

## Install Extension Pack

lets install the extension pack 

* click on `File` on top left corner

* `Prefrences`

* `Extensions`

 ![add-extension](images/add-extension.png)

 ![select-pack](images/select-location.png)

* it will ask u to `install` do it

* scroll and `agree` the license

* click `OK`

we have successfully install extension pack




### Download Centos Image

<a id="centos" href="https://www.centos.org/download/" target="_blank">centos-download</a> 

select the architecture dependent dvd.iso file

![centos-download](images/centos-download.png)


### Install centos 8

* click on add `New`

![New](images/new-box.png)

* Name and Operating System

    - Type Name : `desired name`

    - Machine Folder: `desired location`
    
    - Type: `Linux` 

    - Version : `Red Hat (64-bit)`

![name-and-oprating](images/name-and-operating.png)

* Memory Size

    around `1024MB`

![memory-size](images/ram.png)

* Create virtual hardisk
   - click on `create`

![virtual-disk](images/virtual-disk.png)

* Hardisk file type
    - `VDI (VirtualBox Disk Image)`

![vdi](images/vdi.png)

* Storage on physical hard disk
    - `Dynamically allocated`

![dynamic](images/dynamic.png)

* File location and size
    - leave location as is
    - size : `20 GB`

![disk-size](images/disk-size.png)

You should get something like this

![box-overview](images/box-overview.png)

Change the name by clicking on `Settings` icon at the top center Navbar

![template](images/name-change.png)

* and click `OK`

we will be using this as `base template` for creating `multiple instances` in case our vm breaks or all Hell falls upon us we can always create new one from base template.

`PS:` we could have set the name at the very start but forgot it hence i changed it later

u can also create `snapshots` to save workstate

### Configure Base Template

- click on settings
    - General
        - advanced
            - clipboard & Drag'n'Drop : `Bidirectional`

![bidirectional](images/clipboard.png)

* Boot Order

![boot-order](images/boot-order.png)

* Add DVD Installer

![iso](images/iso.png)

![add-iso](images/add-iso.png)

![select-iso](images/select-iso.png)

![done](images/done.png)

- Click on `Start`

![click-start](images/click-start.png)

- Click on install centos 8

![install-centos8](images/click-install.png)

It might take some time be patient

![language](images/language.png)

click continue

![timezone](images/timezone.png)

click done

![Network](images/network.png)

on it and click done

![install-disk](images/media-install.png)

![root](images/root.png)

click done twice if pass simple

![user](images/user.png)

click done twice

![begin](images/begin.png)

![progress](images/progress.png)

Be patient this might take some time

Restart the box

![accpet-license](images/license.png)

tick mark and click done

![finsih-configuration](images/finish-configuration.png)

* Login with credentials

### Install Guest Editions 

open terminal

and follow these steps

`su`

login as root

```
dnf install -y epel-release
```
<br>

![repo](images/repo.png)

* Install Kernel Headers and Build Tools

```
sudo dnf install -y gcc make perl kernel-devel kernel-headers bzip2 dkms
```

![kernel](images/kernel-headers.png)

```
rpm -q kernel-devel
uname -r
```

might face kernel conflict like i do 

![conflict](images/conflict.png)

To resolve this issue 

```
dnf update kernel-*
```

type `y` on prompt

![resolve](images/resolve.png)

reboot

```
reboot now
```

select the correct kernel during boot

![correct](images/correct.png)

again check kernel info

```
rpm -q kernel-devel
uname -r
```

![noconflict](images/noconflict.png)

```
mount /dev/cdrom /mnt   

./VBoxLinuxAdditions.run
```
<br> 

![guest](images/guest.png)

```
shudown now
```

### Clone The Base Template

* right click on the box select `clone`
    - Full clone

![clone](images/clone.png)

![clonning](images/clonning.png)

### Start Clone

![start-clone](images/start-clone.png)

![dat](images/dat.png)

