+++
title = "Installing Guix System for daily use"
description = "How to install Guix System on a laptop to be used daily"

draft = true

[taxonomies]
tags = ["os", "guix"]

[extra.series_template_variables]
position = "third"
prev_achievement = "we prepared for the installation of Guix System"
curr_objective = "how to execute it"
curr_achievement = "Guix Gystem has been installed"
next_objective = "how to set up a basic system for daily used"
+++

Guix System can be installed either using a Graphical User Interface (GUI) or using the command line through a terminal.

The GUI installation is guided while the command line one is more manual and for more advanced users.

Nevertheless, I feel more at ease with the command line because it gives me more control on what I do and I would expect it to be more reproducible through time.

I'll focus on a manual installation here but both are fine and you can use the one you prefer.
I'll try to explain what I do and not only how to do it so you may be able to execute something similar through the GUI.

Last but not least, please note actual configuration is adapted to my personal needs and alternative configuration may be better suited for you.
Always keep in mind what your personal use case is.

# Launch the installation

Installation can be summarised in the following steps:

1. Configure the BIOS/UEFI
2. Boot on the USB stick and configure basics
3. Partition your disks
4. Configure everything else and launch the installation

## Configure the BIOS/UEFI and boot from the USB stick

BIOS/UEFI is what is executed at the very first when you push the power button of your computer.
BIOS [^1] is older, UEFI [^2] is newer.
UEFI brings many improvements both from a functionality and security perspective so I would tend to prefer it.

Anyway, it is usually shipped with the motherboard by the manufacturer and might not really be privacy compliant.

FOSS alternatives exist.
If you are interested, you can look at [coreboot](https://coreboot.org/) and [libreboot](https://libreboot.org/).
Some privacy respectful manufacturers even ship them with their computer [^3].

Unfortunately, my laptop has a proprietary ASUS UEFI firmware and is not supported by libreboot or alternatives out-of-the-box.
I won't take action on having it supported now.
It would took me far too much time because I would have to strongly grow my competencies on this.
I'll stick to the UEFI already in there.

How to access the BIOS/UEFI can vary from one computer to another as it depends on the motherboard manufacturer.
Usually, you have to hit repeatidly one of the following key as soon as the computer starts: `F1`, `F2`, `F8`, `F10`, `Suppr` or `Esc`.
Please search for how to reach BIOS for your current OS.
There may be many places on the Internet explaining you how to do.
Anyway, if you reach your usual operating system, it means you failed.
Just restart and try again.

At this stage, the only security advice I can give you is to setup a strong password for your BIOS/UEFI.
Accessing the BIOS/UEFI allows an attacker to modify boot parameters (including boot order, secure boot, ...).
This is a very first step in an attempt to protect your laptop against someone having physical access to it (e.g. evil maid attacks [^4]).
It will also make the laptop harder to reuse in case of theft as it may slow down, but not completely prevent, reinstallation of a new system.
Even if today, you may need to contact the manufacturer's support most of the time, older devices can have their BIOS password resetted by acting on the motherboard (removing battery, short-circuit some pins).

Anyway, make sure to have a way to remember this password for sure or store it somewhere safe.
You may not use it everyday so it is pretty easy to forget about it.
Nevertheless, be sure you'll need it someday (e.g. reinstalling a new OS).

In order to set up a password for your BIOS/UEFI, you first need to enter BIOS/UEFI menu so just restart your computer and access it as explained previously.

Each BIOS/UEFI menu is different but setting up a password should be available in the "Security" tab (possibly after switching to the advanced mode if any).
There may be different passwords for different privileges (administrator, user).
What we are looking for here is to set up the password for administrator.
I would advise you to keep other password disabled if any.
Just change them if disabling them is impossible and they have a default password which, for sure, will be known by an attacked.

Last but not least, be careful with the keymap which may default to QWERTY.
It might not be a big deal when typing your password to login as you may just need to hit the same keys as when you set it up.
Nevertheless, this should be taken into account when writting it down somewhere else.

As soon as you set it up, restart your computer and try to enter the BIOS/UEFI menu.
You may be prompted for the password you just set up.
Make sure it works as expected before moving forward (you won't be able to if not the case anyway...).

At this stage, you may be back to the BIOS/UEFI menu.

One more step toward fully protecting the laptop from tampering with the boot sequence is to enable UEFI Secure Boot.
This option may be available in the "Security" tab as well.
Unfortunately, Guix System doesn't support secure boot officially yet even if there is some work in progress [^5].
Anyway, it is better to activate it later to not overcomplexify the first installation.
I'll add a dedicated article to this series if I find a way to set it up properly.

Last step is to configure the boot order.
As expected, the boot order decide in which order BIOS/UEFI will search disks for an Operating System (OS) to boot on.
You should always only have configured the disk where you have your OS on.
This will prevent any other OS to boot, no matter what.
This option should be available in "Boot" tab.

Once your BIOS/UEFI is properly configured, be sure to save changes.

## Boot on the USB stick and configure basics

Make sure the USB stick is plugged in and come back to BIOS/UEFI menu.

We now want to boot on the first partition of the USB stick.  
Mine is called: `UEFI: SanDisk, Partition 1`.

Some BIOS/UEFI let you boot once on any available storage without altering the current configuration.
This feature is usually called "Boot Menu".

If it is not available for your BIOS/UEFI, you have to modify the boot order to boot on the USB stick first.
Don't forget to revert this configuration once installation has been completed.

When booting on the USB stick, we first arrive on the Grub menu to choose between `GNU Guix installation 1.4.0` and `Firmware setup`.

############### TODO: capture boot

Selecting `GNU Guix installation 1.4.0` leads you to Guix System graphical installer by default.
It is available on *TTY1*.

############### TODO: capture TTY1

*TTY2* provides access to offline Guix documentation.

############### TODO: capture TTY2

*TTY3*, *TTY4*, *TTY5* and *TTY6* let you access root shells for manual installation.

############### TODO: capture TTY3

Switching to a specific TTY is achieved by hitting `ctrl-alt-f<TTY#>`, `f<TTY#>` being one of the function keys `F1` to `F6` according to the TTY number.

You can find any usefull information in Guix documentation for [preparing for installation](https://guix.gnu.org/manual/en/html_node/Preparing-for-Installation.html) and [manual installation](https://guix.gnu.org/manual/en/html_node/Manual-Installation.html).

First of all, let's swtich to *TTY3* (`ctrl-alt-f3`) and make sure the keyboard will behave as we expect it to.
Default is QWERTY and you can have a mapping just below.

{{ image_figure(src="img/qwerty-keyboard-layout.webp", raw_path=true, alt="The layout of a QWERTY keyboard") }}

If you need to change it to something else, just use the `loadkeys` command when on *TTY3* to *TTY6*:
{% wide_container() %}
```bash
# Change your keyboard keymap
## For AZERTY
loadkeys fr
# For BEPO
loadkeys fr-bepo
# For QWERTZ
loadkeys de
## ...
## To come back to QWERTY (which is the default)
loadkeys us

# And to list all available keyboard layouts
ls -l -R /run/current-system/profile/share/keymaps/* | less
## Don't forget the current keyboard layout when using `q` to get out from less
```
{% end %}
{% admonition(type="tip", title="Keyboard layout update") %}
Even if it might be obvious for some, `loadkeys` must just be launched once for all *TTY*s to be updated with the correct keyboard layout.
{% end %}

The last remaining support element to configure is the network.

As previsouly explained, I'll rely on Ethernet so I'll focus on wired connection configuration.
You can find explanation on [how to configure wireless connection in Guix documentation](https://guix.gnu.org/manual/en/html_node/Keyboard-Layout-and-Networking-and-Partitioning.html).

Once wire is plugged, just look at your network interfaces with the following command:
{% wide_container() %}
```bash
ip address
#> 1: lo: <LOOPBACK,MULTICAST,UP,LOWER_UP> mtu 6536 qdisc noqueue state UNKNOWN group...
#>     link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
#>     inet 127.0.0.1/8 scope global lo
#>        valid_lft forever preferred_lft forever
#>     inet6 ::1/128 scope host
#>        valid_lft forever preferred_lft forever
#> 2: eth0: <BROADCAST,MULTICAST,DYNAMIC,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group...
#>     link/ether XX:XX:XX:XX:XX:XX brd XX:XX:XX:XX:XX:XX
#>     inet XXX.XXX.XXX.XXX/XX brd XXX.XXX.XXX.XXX scope global eth0
#>        valid_lft forever preferred_lft forever
#>     inet6 XX:XX:XX:XX:XX:XX:XX:XX/XX scope global dynamic mngtmpaddr
#>        ...
```
{% end %}

The network interface is called `eth0` in my case but it might different for you.

The interface's state should be UP as soon as you plug the network cable but if eth0's state is not UP, you can use the following command:
{% wide_container() %}
```bash
ip link set eth0 up
```
{% end %}

After being up, eth0 should have an IP address automatically.
If it is not the case, you can try following commands:
{% wide_container() %}
```bash
# If DHCP is available on the connected network
dhclient -v eth0

# If not, set up an address manually (assuming IPv4)
ip address add XX.XX.XX.XX/XX dev eth0
```
{% end %}

Once everything is configured (which was limited to just plug the network cable in my case), you can check it works by reaching Guix on the Internet:
{% wide_container() %}
```bash
ping -c 3 gnu.org
#> PING gnu.org (209.51.188.116): 56 data bytes
#> 64 bytes from 209.51.188.116: icmp_seq=0 ttl=53 time=96.463ms
#> 64 bytes from 209.51.188.116: icmp_seq=1 ttl=53 time=96.463ms
#> 64 bytes from 209.51.188.116: icmp_seq=2 ttl=53 time=98.373ms
--- gnu.org ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max/stddev = 96.463/97.100/98.373/0.900 ms
```
{% end %}

If the ping command doesn't work, verify your network configuration, maybe you are missing the gateway or the DNS configuration:
{% wide_container() %}
```bash
# Check Gateway configuration
ip route
#> default via YY.YY.YY.YY dev eth0
#> YY.YY.YY.YY dev eth0 scope link

# If previous command is empty, you can add a gateway (assuming gateway's IP is YY.YY.YY.YY)
ip route add YY.YY.YY.YY dev eht0
ip route add default via YY.YY.YY.YY dev eth0

# Check DNS configuration
cat /etc/resolv.conf
#> nameserver <primary-dns>
#> nameserver <secondary-dns>

# If previous command is empty, you can add nameservers by modifying /etc/resolv.conf
vi /etc/resolv.conf
```
{% end %}

Once done, check ping is now working.

Now that the preliminary configuration is done, we can move forward in partitionning the future system

## Partition

{% admonition(type="danger", title="Data will be erased") %}
During this step, we'll partition the disk and erase data it contains with no way to recover it.

**Please make sure data on the disk is backed up or it will be lost forever.**
{% end %}

When speaking storage, I mainly have two concepts in mind: encryption and impermanence.

Encryption is all about making sure the data we keep is safe from prying eyes.

Impermanence is all about making sure we only keep what we want (or at least, what we expect to) and everything else is discarded as soon as we reboot.

############### TODO: introduce what will be created and why

2 partitions:
- EFI -> 
- ROOT -> btrfs


First of all, let's identify the disk we will work on with `lsblk` or `fdisk -l`.
{% wide_container() %}
```bash
lsblk
#> NAME MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
#> sda    8:0    0 465.8G  0 disk
#> sr0   11:0    1  1024M  0 rom
############### TODO: adapt the result
```
{% end %}

The result depends on what is currently on the disk we'll install Guix System on.
Anyway, the only goal here is to identify which device we'll partition.
As I did tests and captures from a virtual machine, you'll see xvda as disk to partition.
Nevertheless, my target installation will use `/dev/sda` so I'll use it in command and explanations.
Last but not least, it may be something different for you so just replace `/dev/sda` or `/dev/xvda` with you device name.

Now we need to partition the disk.
We will use `parted` here but plenty of other tools exist: `fdisk`, `cfdisk`, `sfdisk`, `gdisk`, `cgdisk`.
`parted` has many features but you really need to know where you want to go because it will apply changes on the fly (whereas others may have a write operation).
One of the advantges is that command can be executed from the CLI (i.e even without interaction if option --script if provided).

You can choose the one you prefer as long as you reach the following state:
- GPT partition table type
- Partition 1:
  - Type: EFI ESP
  - Size: ~555MB
  - Filesystem: fat32
- Partition 2:
  - Type: Linux filesystem
  - Size: The rest of the disk
  - Filesystem: btrfs

With parted, you need to execute the following commands:
{% wide_container() %}
```bash
# Create new GPT disklabel
## As this action is highly destructive, parted will prompt for confirmation
parted /dev/sda mklabel gpt
# Add boot partition
parted /dev/sda mkpart boot fat32 2048s 555MiB
parted /dev/sda set 1 esp on
# Add root partition
parted /dev/sda 'unit s print'
#> ...
#>
#> Number  Start  End      Size     File system  Name  Flags
#>  1      2048s  1136639s 1134592s              boot  boot, esp
# Take the end sector of the previous partition (1136639 in my case) + 1 as the first sector of the new partition
parted /dev/sda mkpart root btrfs 1136640s 100%
# Print the result
parted /dev/sda print
```
{% end %}

Once configured, make sure all partitions are aligned in an optimal way:
{% wide_container() %}
```bash
# Check partitions are properly aligned
parted /dev/xvda align-check optimal 1
#> 1 aligned
parted /dev/xvda align-check optimal 2
#> 2 aligned
```
{% end %}

The last step is to create the filesystems:

{% wide_container() %}
```bash
TODO
```
{% end %}



## Configure everything needed and install

################# TODO: substitutes

# First boot

Upgrade channel

# Resources

https://rainbow.chard.org/2013/01/30/how-to-align-partitions-for-best-performance-using-parted/

https://wiki.systemcrafters.net/guix/nonguix-installation-guide/
https://guix.gnu.org/manual/en/html_node/System-Installation.html
https://systemcrafters.net/craft-your-system-with-guix/full-system-install/
https://flossmanuals.net/pub/guix-system-and-libreboot-v2.pdf
https://github.com/yveszoundi/guix-config
https://forum.systemcrafters.net/t/guixsd-and-encrypted-root/1057
https://gitlab.com/slalomsk8er/rde-luks2-unlock-once
https://opensource.com/article/20/10/measured-trusted-boot
https://forum.systemcrafters.net/t/luks-tpm-encryption-without-reinstall/1192/2

https://blastrock.github.io/fde-tpm-sb.html
https://oddlama.org/blog/bypassing-disk-encryption-with-tpm2-unlock/
https://0pointer.net/blog/brave-new-trusted-boot-world.html

https://0pointer.net/blog/authenticated-boot-and-disk-encryption-on-linux.html
https://0pointer.net/blog/brave-new-trusted-boot-world.html

Impermanence:

https://issues.guix.gnu.org/51547
https://elis.nu/blog/2020/05/nixos-tmpfs-as-root/
https://github.com/nix-community/impermanence
https://lists.nongnu.org/archive/html/guix-devel/2023-08/msg00060.html


[^1]: <https://en.wikipedia.org/wiki/BIOS>
[^2]: <https://en.wikipedia.org/wiki/UEFI>
[^3]: <https://doc.coreboot.org/distributions.html>
[^4]: You can look at <https://en.wikipedia.org/wiki/Evil_maid_attack> for a description of the attack and at <http://theinvisiblethings.blogspot.com/2009/10/evil-maid-goes-after-truecrypt.html> for a practical implementation example
[^5]: Allowing full use of secure boot is in progress but requires Guix bootloader process to be rewritten (or at least amended). See:
       - https://issues.guix.gnu.org/68524: Support root encryption and secure boot, moved to 72457
       - https://issues.guix.gnu.org/72457: Rewrite bootloader subsystem, including root encryption and secure boot, blocked by 73202
       - https://issues.guix.gnu.org/73202: Preparation for bootloader rewrite, blocked by 69343
       - https://issues.guix.gnu.org/69343: Simplification of bootloader data structures and procedures
      
