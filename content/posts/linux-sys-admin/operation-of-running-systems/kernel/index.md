---
title: Kernel
date: 2021-04-23
hero: images/random.png
menu:
    sidebar:
        name: Kernel
        identifier: kernel  
        parent: operation-of-running-systems
        weight: 3
---
### About

Linux is only the kernel of the operating system, which includes many other components, such as libraries and applications that interact with the kernel.

The kernel is the essential central component that connects the hardware to the software and manages system resources, such as memory and CPU time allocation among competing applications and services. It handles all connected devices using device drivers, and makes the devices available for operating system use.

A system running only a kernel has rather limited functionality. It will be found only in dedicated and focused embedded devices.

The main responsibilities of the kernel include:

- System initialization and boot up
- Process scheduling
- Memory management
- Controlling access to hardware
- I/O (Input/Output) between applications and storage devices
- Implementation of local and network filesystems
- Security control, both locally (such as filesystem permissions) and over the network
- Networking control.

### Kernel Boot Parameters

![default](images/default.png)

Below you can see an explanation of some of the boot parameters, some of which we have displayed previously:

- root: root filesystem
- ro: mounts root device read-only on boot
- vconsole.keymap: which keyboard to use on the console
- crashkernel: how much memory to set aside for kernel crashdumps
- vconsole.font: which font to use on the console
- rhgb: for graphical boot
- quiet: disables most log messages.
- LANG: is the system language.

By convention, there should be no intentionally hidden or secret parameters. They should all be explained in the documentation and patches to the kernel source with new parameters should always include patches to the documentation file.

The sysctl interface can be used to read and tune kernel parameters at run time:

```shell
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo sysctl -a | less
abi.vsyscall32 = 1
crypto.fips_enabled = 0
debug.exception-trace = 1
debug.kprobes-optimization = 1
dev.cdrom.autoclose = 1
dev.cdrom.autoeject = 0
dev.cdrom.check_media = 0
dev.cdrom.debug = 0
dev.cdrom.info = CD-ROM information, Id: cdrom.c 3.20 2003/12/17
dev.cdrom.info = 
dev.cdrom.info = drive name:            sr1     sr0
dev.cdrom.info = drive speed:           32      32
dev.cdrom.info = drive # of slots:      1       1
dev.cdrom.info = Can close tray:                1       1
dev.cdrom.info = Can open tray:         1       1
dev.cdrom.info = Can lock tray:         1       1
dev.cdrom.info = Can change speed:      1       1
dev.cdrom.info = Can select disk:       0       0
dev.cdrom.info = Can read multisession: 1       1
dev.cdrom.info = Can read MCN:          1       1
dev.cdrom.info = Reports media changed: 1       1
dev.cdrom.info = Can play audio:                1       1
dev.cdrom.info = Can write CD-R:                0       0
dev.cdrom.info = Can write CD-RW:       0       0
dev.cdrom.info = Can read DVD:          1       1
dev.cdrom.info = Can write DVD-R:       0       0
dev.cdrom.info = Can write DVD-RAM:     0       0
:
```

### Kernel Packages

Table lists and describes the core and some add-on kernel packages.

Kernel Package | Description
-------------- | -----------
kernel         | Contains no files, but ensures other kernel packages are accurately installed
kernel-core    | Includes a minimal number of modules to provide core functionality
kernel-devel   | Includes support for building kernel modules
kernel-modules | Contains modules for common hardware devices
kernel-modules-extra| Contains modules for not-so-comon hardware devices
kernel-headers | Includes files to support the interface between the kernel and userspace libraries and programs
kernel-tools   | Includes tools to manipulate the kernel 
kernel-tools-libs | Includes the libraries to support the kernel tools

### Understanding Kernel Directory Structure
Kernel and its support files are stored at different locations in the directory hierarchy, of which three locations /boot, /proc, and usr/lib/modules are noteworthy.

### The /boot location

/boot is essentially a file system that is created at system installation. it houses the Linux kernel, GRUB(v?) config, and other kernel and boot support files.

listing. 

```shell
[s0x45ker--_(+_+)_--SysAdmin ~]$ ls -l /boot
total 283888
-rw-r--r--. 1 root root    189466 Apr  9 00:39 config-4.18.0-240.22.1.el8_3.x86_64
-rw-r--r--. 1 root root    189494 Sep 26  2020 config-4.18.0-240.el8.x86_64
drwxr-xr-x. 3 root root        17 Apr 22 02:25 efi
drwx------. 4 root root        83 Apr 23 19:44 grub2
-rw-------. 1 root root 105461933 Apr 22 02:31 initramfs-0-rescue-b20442256ef84cd0b34bdb7cc26027a8.img
-rw-------. 1 root root  53609260 Apr 22 03:37 initramfs-4.18.0-240.22.1.el8_3.x86_64.img
-rw-------. 1 root root  19515350 Apr 22 03:22 initramfs-4.18.0-240.22.1.el8_3.x86_64kdump.img
-rw-------. 1 root root  55612085 Apr 22 02:34 initramfs-4.18.0-240.el8.x86_64.img
-rw-------. 1 root root  19515551 Apr 22 02:35 initramfs-4.18.0-240.el8.x86_64kdump.img
drwxr-xr-x. 3 root root        21 Apr 22 02:29 loader
-rw-------. 1 root root   4034919 Apr  9 00:39 System.map-4.18.0-240.22.1.el8_3.x86_64
-rw-------. 1 root root   4032815 Sep 26  2020 System.map-4.18.0-240.el8.x86_64
-rwxr-xr-x. 1 root root   9514120 Apr 22 02:30 vmlinuz-0-rescue-b20442256ef84cd0b34bdb7cc26027a8
-rwxr-xr-x. 1 root root   9485448 Apr  9 00:39 vmlinuz-4.18.0-240.22.1.el8_3.x86_64
-rwxr-xr-x. 1 root root   9514120 Sep 26  2020 vmlinuz-4.18.0-240.el8.x86_64
[s0x45ker--_(+_+)_--SysAdmin ~]$ 
```
the *vmlinuz*  is the main kernel file with *initramfs* , *config* and *System.map*  storing the main kernel's boot image, configuration and mapping respectively.

The files for rescue version have the string "rescue" embedded in their names, as indicated in the above output.

The efi and grub2  are subdirectories under the /boot 
its listing as such:

```shell
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo ls -l /boot/grub2/
total 28
-rw-r--r--. 1 root root   64 Apr 22 02:33 device.map
drwxr-xr-x. 2 root root   25 Apr 22 02:33 fonts
-rw-r--r--. 1 root root 6583 Apr 22 02:33 grub.cfg
-rw-------. 1 root root 1024 Apr 23 19:44 grubenv
drwxr-xr-x. 2 root root 8192 Apr 22 02:33 i386-pc
[s0x45ker--_(+_+)_--SysAdmin ~]$ 
```

info regarding config for running and rescue kernels exists within 
```shell
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo ls -l /boot/loader/entries/
total 12
-rw-r--r--. 1 root root 389 Apr 22 02:31 b20442256ef84cd0b34bdb7cc26027a8-0-rescue.conf
-rw-r--r--. 1 root root 351 Apr 22 03:17 b20442256ef84cd0b34bdb7cc26027a8-4.18.0-240.22.1.el8_3.x86_64.conf
-rw-r--r--. 1 root root 317 Apr 22 02:31 b20442256ef84cd0b34bdb7cc26027a8-4.18.0-240.el8.x86_64.conf
```

### The /usr/lib/modules Location

list of info about packages within the system

```shell
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo ls -l /usr/lib/modules
total 8
drwxr-xr-x. 3 root root   19 Apr 22 02:26 4.18.0-187.el8.x86_64
drwxr-xr-x. 7 root root 4096 Apr 23 19:41 4.18.0-240.22.1.el8_3.x86_64
drwxr-xr-x. 6 root root 4096 Apr 22 02:57 4.18.0-240.el8.x86_64
[s0x45ker--_(+_+)_--SysAdmin ~]$ 
```

select the kernel being used
```shell
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo ls -l /usr/lib/modules/4.18.0-240.el8.x86_64/
bls.conf             modules.builtin      modules.networking   System.map
build                modules.builtin.bin  modules.order        updates/
config               modules.dep          modules.softdep      vdso/
kernel/              modules.dep.bin      modules.symbols      vmlinuz
modules.alias        modules.devname      modules.symbols.bin  .vmlinuz.hmac
modules.alias.bin    modules.drm          source               weak-updates/
modules.block        modules.modesetting  symvers.gz           
[s0x45ker--_(+_+)_--SysAdmin ~]$ sudo ls -l /usr/lib/modules/4.18.0-240.el8.x86_64/kernel/drivers/
acpi/       dax/        hwmon/      media/      nvme/       pwm/        uwb/
ata/        dca/        hwtracing/  memstick/   parport/    remoteproc/ vdpa/
bcma/       dma/        i2c/        message/    pci/        rtc/        vfio/
block/      edac/       iio/        mfd/        pcmcia/     scsi/       vhost/
bluetooth/  firewire/   infiniband/ misc/       pinctrl/    spi/        video/
cdrom/      firmware/   input/      mmc/        platform/   target/     virtio/
char/       gpio/       iommu/      mtd/        power/      thermal/    watchdog/
cpufreq/    gpu/        isdn/       net/        powercap/   tty/        xen/
cpuidle/    hid/        leds/       ntb/        pps/        uio/        
crypto/     hv/         md/         nvdimm/     ptp/        usb/        
```

There are a number of utility programs that are used with kernel modules:

<details>
<summary>
<b>lsmod</b>
</summary>
<p>
List loaded modules.
    
    [s0x45ker--_(+_+)_--SysAdmin ~]$ lsmod
    Module                  Size  Used by
    binfmt_misc            20480  1
    nls_utf8               16384  2
    isofs                  45056  2
    uinput                 20480  0
    vboxvideo              45056  0
    xt_CHECKSUM            16384  1
    ipt_MASQUERADE         16384  3
    xt_conntrack           16384  1
    ipt_REJECT             16384  2
    nf_nat_tftp            16384  0
    nft_objref             16384  1
    nf_conntrack_tftp      16384  3 nf_nat_tftp
    nft_counter            16384  33
    tun                    53248  1
    bridge                192512  0
    stp                    16384  1 bridge
    llc                    16384  2 bridge,stp
    nft_fib_inet           16384  1
    nft_fib_ipv4           16384  1 nft_fib_inet
    nft_fib_ipv6           16384  1 nft_fib_inet
    nft_fib                16384  3 nft_fib_ipv6,nft_fib_ipv4,nft_fib_inet
    nft_reject_inet        16384  5
    nf_reject_ipv4         16384  2 nft_reject_inet,ipt_REJECT
    nf_reject_ipv6         16384  1 nft_reject_inet
    nft_reject             16384  1 nft_reject_inet
    nft_ct                 20480  18
    nf_tables_set          49152  20
    nft_chain_nat          16384  12
    nf_nat                 45056  3 ipt_MASQUERADE,nf_nat_tftp,nft_chain_nat
    nf_conntrack          172032  6 xt_conntrack,nf_nat,nf_conntrack_tftp,nft_ct,ipt_MASQUERADE,nf_nat_tftp
    nf_defrag_ipv6         20480  1 nf_conntrack
    nf_defrag_ipv4         16384  1 nf_conntrack
    ip6_tables             32768  0
    nft_compat             20480  16
    ip_set                 49152  0
    nf_tables             167936  420 nft_ct,nft_compat,nft_reject_inet,nft_fib_ipv6,nft_objref,nft_fib_ipv4,nft_counter,nft_chain_nat,nf_tables_set,nft_reject,nft_fib,nft_fib_inet
    nfnetlink              16384  4 nft_compat,nf_tables,ip_set
    sunrpc                479232  1
    intel_rapl_msr         16384  0
    intel_rapl_common      24576  1 intel_rapl_msr
    intel_pmc_core_pltdrv    16384  0
    intel_pmc_core         28672  0
    intel_powerclamp       16384  0
    crct10dif_pclmul       16384  1
    snd_intel8x0           45056  7
    crc32_pclmul           16384  0
    snd_ac97_codec        143360  1 snd_intel8x0
    ac97_bus               16384  1 snd_ac97_codec
    snd_seq                81920  0
    snd_seq_device         16384  1 snd_seq
    ghash_clmulni_intel    16384  0
    snd_pcm               118784  2 snd_intel8x0,snd_ac97_codec
    intel_rapl_perf        20480  0
    pcspkr                 16384  0
    snd_timer              40960  2 snd_seq,snd_pcm
    joydev                 24576  0
    snd                    94208  20 snd_seq,snd_seq_device,snd_intel8x0,snd_timer,snd_ac97_codec,snd_pcm
    soundcore              16384  1 snd
    i2c_piix4              24576  0
    video                  49152  0
    ip_tables              28672  0
    xfs                  1511424  2
    libcrc32c              16384  3 nf_conntrack,nf_nat,xfs
    sd_mod                 53248  3
    sr_mod                 28672  2
    cdrom                  65536  1 sr_mod
    sg                     40960  0
    ata_generic            16384  0
    vmwgfx                364544  4
    drm_kms_helper        217088  2 vmwgfx,vboxvideo
    syscopyarea            16384  1 drm_kms_helper
    sysfillrect            16384  1 drm_kms_helper
    sysimgblt              16384  1 drm_kms_helper
    fb_sys_fops            16384  1 drm_kms_helper
    ttm                   110592  2 vmwgfx,vboxvideo
    drm                   557056  8 vmwgfx,drm_kms_helper,vboxvideo,ttm
    ahci                   40960  2
    ata_piix               36864  2
    libahci                40960  1 ahci
    crc32c_intel           24576  1
    serio_raw              16384  0
    vboxguest             385024  6
    e1000                 151552  0
    libata                270336  4 ata_piix,libahci,ahci,ata_generic
    dm_mirror              28672  0
    dm_region_hash         20480  1 dm_mirror
    dm_log                 20480  2 dm_region_hash,dm_mirror
    dm_mod                151552  8 dm_log,dm_mirror
    fuse                  131072  3

</p>
</details>

<details>
<summary>
<b>insmod</b>
</summary>
<p>
Directly load modules.
    
    [s0x45ker--_(+_+)_--SysAdmin ~]$ sudo insmod /usr/lib/modules/4.18.0-240.22.1.el8_3.x86_64/kernel/net/ipv4/netfilter/ip_tables.ko.xz
    insmod: ERROR: could not insert module /usr/lib/modules/4.18.0-240.22.1.el8_3.x86_64/kernel/net/ipv4/netfilter/ip_tables.ko.xz: File exists    
</p>
</details>

<details>
<summary>
<b>rmmod</b>
</summary>
<p>
Directly remove modules.
    
    [s0x45ker--_(+_+)_--SysAdmin ~]$ lsmod | grep ip_tables
    ip_tables              28672  0
    [s0x45ker--_(+_+)_--SysAdmin ~]$ sudo rmmod ip_tables
    [s0x45ker--_(+_+)_--SysAdmin ~]$ lsmod | grep ip_tables
</p>
</details>

<details>
<summary>
<b>modprobe</b>
</summary>
<p>
Load or unload modules, using a pre-built module database with dependency and location information.
    
    [s0x45ker--_(+_+)_--SysAdmin ~]$ sudo modprobe ip_tables
    [s0x45ker--_(+_+)_--SysAdmin ~]$ lsmod | grep ip_tables
    ip_tables              28672  0

    [s0x45ker--_(+_+)_--SysAdmin ~]$ sudo modprobe -r ip_tables
    [s0x45ker--_(+_+)_--SysAdmin ~]$ lsmod | grep ip_tables
    [s0x45ker--_(+_+)_--SysAdmin ~]$ 
</p>
</details>
  
<details>
<summary>
<b>depmod</b>
</summary>
<p>
 Rebuild the module dependency database.
    
    [s0x45ker--_(+_+)_--SysAdmin ~]$ sudo depmod 
    [s0x45ker--_(+_+)_--SysAdmin ~]$
</p>
</details>

  
<details>
<summary>
<b>modinfo</b>
</summary>
<p>
 Display information about a module.
    
    [s0x45ker--_(+_+)_--SysAdmin ~]$ sudo modinfo ip_tables
    filename:       /lib/modules/4.18.0-240.22.1.el8_3.x86_64/kernel/net/ipv4/netfilter/ip_tables.ko.xz
    alias:          ipt_icmp
    description:    IPv4 packet filter
    author:         Netfilter Core Team <coreteam@netfilter.org>
    license:        GPL
    rhelversion:    8.3
    srcversion:     18BB21BB7835F901AA8B42A
    depends:        
    intree:         Y
    name:           ip_tables
    vermagic:       4.18.0-240.22.1.el8_3.x86_64 SMP mod_unload modversions 
    sig_id:         PKCS#7
    signer:         CentOS kernel signing key
    sig_key:        78:2B:A1:1C:2F:DE:D4:A5:85:15:10:61:C8:2E:D9:98:9C:D7:4D:14
    sig_hashalgo:   sha256
    signature:      57:8B:BC:89:CF:88:DB:AB:33:58:1A:AF:B3:2E:0F:93:E5:E7:8C:A9:
		            08:09:4B:D9:0F:88:64:AA:3D:EF:2C:28:83:CF:5E:21:3E:81:66:DC:
		            21:A9:94:8D:90:D2:D9:92:00:9A:E0:DB:E0:5E:FA:B8:0A:FB:B2:05:
                    67:5A:43:EA:B3:61:AC:8E:5E:F1:38:71:E6:21:2C:FA:6E:31:CA:00:
                    C3:61:C0:00:10:A7:CF:BF:B9:B6:5B:2B:EE:25:90:08:6F:CD:0B:0C:
                    89:4F:8C:38:4C:39:1A:4F:BA:D1:7F:61:66:AC:7B:91:41:91:78:C8:
                    22:EA:9F:BF:C2:A8:71:0D:A0:85:A2:90:29:82:9F:AB:AE:23:5F:95:
                    0C:5E:60:73:9D:4A:6E:DA:DF:BA:89:01:9C:8E:4D:4D:5C:41:3D:88:
                    44:3D:79:3A:EC:1E:A9:52:BB:F1:4A:6B:C8:DB:DE:70:67:7C:00:CD:
                    88:6A:A4:B9:DB:AB:C7:83:EA:BA:EF:B8:F1:30:FD:53:3A:3D:EB:EE:
                    A1:0F:08:14:C8:0D:EE:6E:32:9B:3D:0C:24:2A:69:E7:64:61:60:6F:
                    0E:57:54:CB:93:A8:86:CF:53:92:74:23:EE:87:01:34:4F:77:62:E6:
                    D3:8F:E8:E7:37:18:ED:45:FC:C6:48:95:1A:DF:A3:72:94:E4:0D:7C:
                    00:E2:40:D9:5A:45:CE:7A:CE:01:03:C2:F2:AE:35:FB:F0:EC:5F:41:
                    73:94:CF:F2:64:16:FF:79:76:65:13:42:4C:91:3B:5A:C4:C3:D3:E5:
                    6F:74:80:93:17:E5:D5:65:A5:22:87:07:40:DA:B9:F4:81:C7:BC:AF:
                    49:B2:5D:62:CD:42:52:E3:CC:0E:D6:95:26:B4:7C:FD:A5:CB:B2:9E:
                    7C:07:38:F7:9C:86:D8:F9:90:FB:5A:7E:03:DB:3F:6E:15:64:70:84:
                    28:47:E5:6F:FE:A7:8F:DB:8D:18:97:13:62:38:8C:C1:ED:14:15:81:
                    D4:53:7A:77 
</p>
</details>
  
There are some important things to keep in mind when loading and unloading modules:

- It is impossible to unload a module being used by one or more other modules, which one can ascertain from the lsmod listing.
- It is impossible to unload a module that is being used by one or more processes, which can also be seen from the lsmod listing. However, there are modules which do not keep track of this reference count, such as network device driver modules, as it would make it too difficult to temporarily replace a module without shutting down and restarting much of the whole network stack.
- When a module is loaded with modprobe, the system will automatically load any other modules that need to be loaded first.
- When a module is unloaded with modprobe -r, the system will automatically unload any other modules being used by the module, if they are not being simultaneously used by any other loaded modules.
