+++
title = "Format a USB stick for Linux and Windows"
description = "How to prepare a USB stick on Linux to be used with both Linux and Windows (and probably macOS as well)"

[taxonomies]
tags = ["linux"]
+++

It can be useful to share things between two different computers using a USB stick.
If they both run the same operating system, there is no major issue.
Nevertheless, if one is running Linux and the other is running Windows, you need to be careful with the filesystem you'll use to format the USB stick.
You need to pick up a filesystem that can be read by the two operating systems.

Long story short, give `exFAT` a try [^1].

`exFAT` [^2] is a filesystem designed by Microsoft in 2006 and specifically optimized for flash memory such as USB sticks and SD cards.
It has been adopted by the SD Association as the default file system for some SD cards larger than 32GB.
It is not only supported by Windows (since Windows Vista SP1 and Windows Server 2008) but also by macOS (since 10.6.5) and Linux (since kernel 5.4 and even before via FUSE).

# Can you format a partition with `exFAT`?

Linux kernel 5.4 was released in November 2019.
I will assume your system is up-to-date and focus on using `exFAT` native support by the Linux kernel.
If it is not the case, I would strongly advise upgrading your Linux kernel to a maintained version.
At the time of writing, all the long-term release kernels have `exFAT` support (post kernel 5.4 release).

In order to ensure `exFAT` tools are there, just check if `mkfs.exfat` is present on your system.

If using `exFAT` to format a partition is possible, you'll have the following response:
{% wide_container() %}
```bash
which mkfs.exfat
#> /usr/sbin/mkfs.exfat
```
{% end %}

Otherwise, you'll have something like the following response:
{% wide_container() %}
```bash
which mkfs.exfat
#> which: no mkfs.exfat in (/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin)
```
{% end %}

Beware that your path may vary depending on the Linux system you're running on.

In case `exFAT` tools are missing, just install them according to your system:
{% wide_container() %}
```bash
sudo apt install exfatprogs # Debian/Ubuntu
sudo dnf install exfatprogs # Red Hat/Fedora/CentOS
sudo pacman -S exfatprogs # Arch Linux/Manjaro
sudo moss install exfatprogs # AerynOS
```
{% end %}

Now that `exFAT` tools are available, let's partition the USB stick.

# Identify the USB stick

Before partitioning the USB stick, you need first to know its *device name*.

Usually, using `lsblk` is enough to identify your USB stick with the following command:
{% wide_container() %}
```bash
lsblk -o name,vendor,model,size,type,tran,mountpoint
#> NAME                                          VENDOR   MODEL                       SIZE TYPE  TRAN   MOUNTPOINT
#> sdX                                           ATA      Micron_1100_MTFDDAV256TBN 238,5G disk  sata
#> ├─sdX1                                                                             554M part
#> ├─sdX2                                                                               4G part
#> └─sdX3                                                                             234G part
#>   └─luks-redacted-0000-0000-0000-000000000000                                      234G crypt        /
#> sdY                                           SanDisk  Ultra Fit                  57,3G disk  usb
#> ├─sdY1                                                                               2G part
#> └─sdY2                                                                             173M part
#> zram0                                                                              7,6G disk         [SWAP]
```
{% end %}

The USB stick I use is a 60GB SanDisk Ultra Fit, so I know its *device name* is `sdY`.
Beware that yours may differ.

If it is impossible to identify the stick thanks to this command, we can use `dmesg` by:
1. Unplugging the USB stick
2. Running the command `sudo dmesg --follow-new` to display all the upcoming messages
3. Plugging in the USB stick

Once plugged in, you should retrieve messages similar to the following ones:
{% wide_container() %}
```bash
sudo dmesg --follow-new
#> [ <timestamp>] usb 2-2: new SuperSpeed USB device number 3 using xhci_hcd
#> [ <timestamp>] usb 2-2: New USB device found, idVendor=0781, idProduct=5583, bcdDevice= 1.00
#> [ <timestamp>] usb 2-2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
#> [ <timestamp>] usb 2-2: Product: Ultra Fit
#> [ <timestamp>] usb 2-2: Manufacturer: SanDisk
#> [ <timestamp>] usb 2-2: SerialNumber: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> [ <timestamp>] usb-storage 2-2:1.0: USB Mass Storage device detected
#> [ <timestamp>] scsi host1: usb-storage 2-2:1.0
#> [ <timestamp>] scsi 1:0:0:0: Direct-Access     SanDisk  Ultra Fit        1.00 PQ: 0 ANSI: 6
#> [ <timestamp>] sd 1:0:0:0: [sdY] 120176640 512-byte logical blocks: (61.5 GB/57.3 GiB)
#> [ <timestamp>] sd 1:0:0:0: [sdY] Write Protect is off
#> [ <timestamp>] sd 1:0:0:0: [sdY] Mode Sense: 43 00 00 00
#> [ <timestamp>] sd 1:0:0:0: [sdY] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
#> [ <timestamp>]  sdY: sdY1 sdY2
#> [ <timestamp>] sd 1:0:0:0: [sdY] Attached SCSI removable disk
```
{% end %}

We can extract the *device name* `sdY` from the messages.

# Partition the USB stick

Now that we've found the device we need to deal with, let's partition it.

{% admonition(type="danger") %}
Partitioning a disk will make all its data inaccessible.
Recovering it might be possible but it is complex with no guarantee of success.
Please back up your data before partitioning the USB stick if you want to keep it.
{% end %}

First, we need to choose the layout of the partition table between [MBR](<https://en.wikipedia.org/wiki/Master_boot_record>) and [GPT](<https://en.wikipedia.org/wiki/GUID_Partition_Table>).

You'll find many comparisons between the two partition tables on the Internet [^3].
To make it simple, MBR is older, may have broader compatibility with older operating systems and is limited to 4 primary partitions for a total size of 2TB maximum.
GPT is newer, more robust, has unlimited partitions and can manage up to 9.4 ZB of storage.

In addition, GPT is only supported by modern UEFI firmware for booting while MBR may be able to boot almost everywhere.
We don't care here as we just focus on transferring data between Linux and Windows.

Being more recent, I thought GPT would be more suitable.
Nevertheless, after several tests, I noticed that using a GPT partition table prevents proper detection of the USB stick on the Windows system I played with.
It is not accessible from the Explorer even if we can see it in the disk management.

Tests just consist of partitioning and formatting the USB stick as described below from a Linux system, putting a dummy text file on it, unplugging it, plugging it into a Windows system and trying to retrieve the file.

Last but not least, if I plug the USB stick having a GPT partition table into Windows, destroy the partition, recreate it and format it with exFAT, then it works...
The USB stick is properly recognized by the Windows system and remains accessible by the Linux system.
I may be missing some options in the partition creation for GPT in order to have it properly handled by Windows.

The next two parts will describe the use of each partition table.

You only need to apply one and I would advise you [to go with MBR](#mbr-partition).

## Use an MBR partition table {#mbr-partition}

{% admonition(type="warning") %}
This is the partition table I recommend according to tests I made.
Skip this part if you want to use a [GPT partition table](#gpt-partition) anyway.
{% end %}

First, create the disk:
{% wide_container() %}
```bash
sudo parted /dev/sdY mklabel msdos
#> Warning: The existing disk label on /dev/sdY will be destroyed and all data on this disk will be lost. Do you want to continue?
#> Yes/No? yes
#> Information: You may need to update /etc/fstab.
```
{% end %}

Then, create the unique partition that uses the entire USB stick:
{% wide_container() %}
```bash
sudo parted /dev/sdY mkpart primary ntfs 0% 100%
#> Information: You may need to update /etc/fstab.
```
{% end %}

We can use `ntfs` instead of `exfat` as `parted` doesn't propose it as a filesystem.

Now that the partition is created, we just need to [format it](#format-partition).

## Use a GPT partition table {#gpt-partition}

{% admonition(type="warning") %}
I DO NOT recommend this partition table according to tests I made.
Unless you know what you're doing, I would advise to use an [MBR partition table](#mbr-partition) instead.
{% end %}

First, create the disk:
{% wide_container() %}
```bash
sudo parted /dev/sdY mklabel gpt
#> Warning: The existing disk label on /dev/sdY will be destroyed and all data on this disk will be lost. Do you want to continue?
#> Yes/No? yes
#> Information: You may need to update /etc/fstab.
```
{% end %}

Then, create the unique partition that uses the entire USB stick:
{% wide_container() %}
```bash
sudo parted /dev/sdY mkpart ntfs 0% 100%
#> Information: You may need to update /etc/fstab.
```
{% end %}

We can use `ntfs` instead of `exfat` as `parted` doesn't propose it as a filesystem.

Now that the partition is created, we just need to [format it](#format-partition).

## Format the partition with `exFAT` {#format-partition}

{% wide_container() %}
```bash
sudo mkfs.exfat /dev/sdY1 -L 'Data'
#> exfatprogs version : 1.3.0
#> Creating exFAT filesystem(/dev/sdY1, cluster size=131072)
#> 
#> Writing volume boot record: done
#> Writing backup volume boot record: done
#> Fat table creation: done
#> Allocation bitmap creation: done
#> Upcase table creation: done
#> Writing root directory entry: done
#> Synchronizing...
#> 
#> exFAT format complete!
```
{% end %}

The partition has been labelled `Data` but you can change it to whatever you want.
It must be short (limited to 11 characters) and I would keep it without spaces or special characters as I am not sure how it would be handled by the different operating systems.

You can check everything went well with the following command:
{% wide_container() %}
```bash
lsblk -o name,vendor,model,size,type,tran,fstype,label /dev/sdY
NAME   VENDOR   MODEL      SIZE TYPE TRAN FSTYPE  LABEL
sdY    SanDisk  Ultra Fit 57,3G disk usb  iso9660
└─sdY1                    57,3G part      exfat   Data
```
{% end %}

You can now use your USB stick both on Linux and Windows (and probably on macOS but I cannot test it).

{{ image_figure(src="img/usb-stick-linux.webp", raw_path=true, alt="The USB stick detected on a computer running a Linux operating system (AerynOS)") }}

{{ image_figure(src="img/usb-stick-windows.webp", raw_path=true, alt="The USB stick detected on a computer running a Windows operating system (Windows 11)") }}

[^1]: <https://askubuntu.com/questions/1281698/what-is-the-best-way-to-format-a-usb-stick-such-that-it-can-be-used-with-both-li>
[^2]: <https://en.wikipedia.org/wiki/ExFAT>
[^3]: Searching for "MBR vs GPT" or "MBR vs GPT usb drive" might be enough.  
I found this reference interesting: <https://darwinsdata.com/what-type-of-partition-for-usb-drive/>

