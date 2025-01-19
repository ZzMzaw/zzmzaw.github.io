+++
title = "Preparing for Guix System installation"
description = "How to be ready to install Guix System on a laptop, from checking hardware to preparing the installation media"

[taxonomies]
tags = ["os", "guix"]

[extra.series_template_variables]
position = "second"
prev_achievement = "we have chosen Guix System as a new operating system"
curr_objective = "how to prepare its installation"
curr_achievement = "we are ready to install Guix System"
next_objective = "how to do it for real"
+++

[Guix documentation](https://guix.gnu.org/manual/) is pretty well organized and complete.
It exists in multiple languages and formats.

The interesting point is that it contains [a whole section on how to install Guix System](<https://guix.gnu.org/manual/en/html_node/System-Installation.html>).

Before starting, I strongly advise reading the [limitations](https://guix.gnu.org/manual/en/html_node/Limitations.html) and [hardware considerations](https://guix.gnu.org/manual/en/html_node/Hardware-Considerations.html).
Guix strictly adhere to the "Free Software Definition" [^1].
This means any non-free software won't be supported nor even considered worth discussing.
This point of view must be respected when interacting with teams maintaining Guix.

Nevertheless, adding support for non-free software is possible.
For Guix, it is mostly done thanks to [the Nonguix repository](https://gitlab.com/nonguix/nonguix).

I would not be able to run Guix System on my laptop otherwise.
I bought it a couple of years ago when I was far less aware about Free Software, but it is still working well until now.
Consequently, I don't want to replace it yet.

# Hardware considerations

Before going further, I want to know better the laptop I have in order to make sure it will be able to support what I want to do.
This paragraph will be updated according to all future articles to have one single place where all hardware requirements are listed to reach the end of the series.

I currently use an ASUS Notebook UX330U with a Core i7 7500 as CPU, 8GB of RAM and about 500GB of SSD storage.
Linux is already installed on it, so I know compatibles drivers exist for the most important hardware components.

## Will it work with free software only?

There is no chance that such an old laptop provided by a commercial brand such as Asus will be certified by the [Respect Your Freedom program](https://ryf.fsf.org/).
If you're looking for a new laptop, it might be a good starting point, even if the laptop list is rather limited for now.
The vendors' list can be used to browse more products and jump to others similar vendors.
[Minifree Ltd](https://minifree.org/), not listed there at the time of writing, is worth mentioning even if I never used any of their product myself.
Best solution for those who want to buy a new laptop to install Guix System on remains to browse the Internet with your favorite search engine and look for recent people feedbacks about it.

If, like me, you already have a laptop, you can [search h-node](https://www.h-node.org/search/form/en) to check how much it is compatible with free software.

{{ image_figure(src="img/h-node-search.en.webp", raw_path=true, alt="The h-node search page", full_width=true) }}

Using the search capabilities is pretty straightforward:
- Just copy and paste the output of `lspci -vmmnn` in the *analyze the output of the lspci command* (as explained on the page) and it will list what has been identified and if it is compatible.
You can still click on the model name to get more details.
- Just copy and paste the `<vendorid>:<productid>` from the output of `lsusb` in the *search by vendorid:productid* and it will let you know if it is in h-node database.
Clicking on the model name gives you the details and if it is compatible.

Doing so, you can verify supports for Wi-Fi adapter, USB controller, Bluetooth interface, Webcam, Fingerprint reader, ...

Wi-Fi adapter and USB controllers are particularly important to check as you'll need network during the installation to download packages.
Everything else can be fixed after the installation if Linux compatible drivers exist.

Anyway, the Guix System installer warns you in case it detects devices lacking of free firmware or free drivers.

In my case, I already know that the Wi-Fi adapter is not compatible with free software:

{{ image_figure(src="img/h-node-lspci-search-result.en.webp", raw_path=true, alt="The h-node lspci search result showing my Wi-Fi adapter is not free-compatible", full_width=true) }}

Nevertheless, I have a USB Ethernet adapter which is compatible:

{{ image_figure(src="img/h-node-lsusb-search-result.en.webp", raw_path=true, alt="The h-node lsusb search result showing my USB Ethernet adapter is free-compatible", full_width=true) }}

I planned to use it anyway during the installation, as Ethernet is faster than Wi-Fi at my place.

# Preparation of the installation media

The preparation of the installation media is made simple by following the section dedicated to [USB stick and DVD installation](https://guix.gnu.org/manual/en/html_node/USB-Stick-and-DVD-Installation.html) from the Guix manual.

We can sum it up in 2 steps:

1. Get the installation image
2. Copy it to a USB stick

## Get the installation image

There are multiple ways to retrieve a Guix System installation image.
Choosing the method you trust more is up to you according to your threat model.
Personally, I preferred the official one, as I had an alternative to the incompatible Wi-Fi adapter.

An OS image downloaded from the Internet should only be used after signature verification to make sure it has not been tampered with since it has been generated.
You can refer to [the dedicated article about signature verification](@/posts/2024-12-29_verifying-openpgp-signature/index.md) for more details.

### Download the official image

The official image can be retrieved from <https://guix.gnu.org/en/download/>.

It is built by the team maintaining Guix and is the base for any alternative images.
It may be your preferred choice, except if you need non-free software for your network and you don't have any workaround.

Just pick up the last `guix-system-install` version for your architecture (both `x86_64` and `i686` are supported) with the associated signature file (same name with `.sig` added).
At the time of writing, it is:
* <https://ftpmirror.gnu.org/gnu/guix/guix-system-install-1.4.0.x86_64-linux.iso>
* <https://ftpmirror.gnu.org/gnu/guix/guix-system-install-1.4.0.x86_64-linux.iso.sig>

{% admonition(type="tip", title="Use the original repository") %}
<https://ftpmirror.gnu.org/gnu/guix/> redirects you to the closest mirror.  
If you have any doubt about the mirror trustworthiness from a privacy and/or security perspective or the way you download doesn't support redirections, just use <https://ftp.gnu.org/gnu/guix/> instead.  
This is the main repository provided by those maintaining Guix.
{% end %}

Once image and signature files are downloaded, you can try to verify the signature before importing the key:
{% wide_container() %}
```bash
gpg --verify 'guix-system-install-1.4.0.x86_64-linux.iso.sig'
#> gpg: assuming signed data in 'guix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Sun Dec 18 22:09:26 2022 CET
#> gpg:                using RSA key 3CE464558A84FDC69DB40CFB090B11993D9AEBB5
#> gpg: Can't check signature: No public key
```
{% end %}

Let's download the key as indicated in the [Guix System documentation](https://guix.gnu.org/manual/en/html_node/USB-Stick-and-DVD-Installation.html):
{% wide_container() %}
```bash
wget 'https://sv.gnu.org/people/viewgpg.php?user_id=15145' -O 'guix-system-install-key.gpg'
gpg --show-keys 'guix-system-install-key.gpg'
#> pub   rsa4096 2014-08-11 [SC] [expires: 2025-04-11]
#>       3CE464558A84FDC69DB40CFB090B11993D9AEBB5
#> uid                      Ludovic Courtès <ludo@gnu.org>
#> uid                      Ludovic Courtès <ludo@chbouib.org>
#> uid                      Ludovic Courtès (Inria) <ludovic.courtes@inria.fr>
#> sub   rsa4096 2014-08-11 [E]
```
{% end %}

Key fingerprint is the same, so this is the key which signed the image.

The key claims to be owned by Ludovic Courtès, but there is no way to confirm it through the documentation.

Searching `Ludovic Courtès` with your favorite search engine should lead you to his Inria page among the top results: <https://people.bordeaux.inria.fr/lcourtes/>.

Inria is the French National Institute for Research in Digital Science and Technology, and I would tend to trust it.

Ludovic Courtès provides his GPG key's fingerprint on his page, and it matches the one of the key just above:

{{ image_figure(src="img/ludovic-courtes-inria-page.webp", raw_path=true, alt="The Inria page for Ludovic Courtès", full_width=true) }}

Having two different sources pointing to the same key, I assume I can trust the key and so import it:
{% wide_container() %}
```bash
gpg --import 'guix-system-install-key.gpg' 
#> gpg: key 090B11993D9AEBB5: 127 signatures not checked due to missing keys
#> gpg: key 090B11993D9AEBB5: public key "Ludovic Courtès <ludo@gnu.org>" imported
#> gpg: Total number processed: 1
#> gpg:               imported: 1
#> gpg: no ultimately trusted keys found
```
{% end %}

Once the key is imported, we can now verify that the signature is valid:
{% wide_container() %}
```bash
gpg --verify 'guix-system-install-1.4.0.x86_64-linux.iso.sig' 
#> gpg: assuming signed data in 'guix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Sun Dec 18 22:09:26 2022 CET
#> gpg:                using RSA key 3CE464558A84FDC69DB40CFB090B11993D9AEBB5
#> gpg: Good signature from "Ludovic Courtès <ludo@gnu.org>" [unknown]
#> gpg:                 aka "Ludovic Courtès <ludo@chbouib.org>" [unknown]
#> gpg:                 aka "Ludovic Courtès (Inria) <ludovic.courtes@inria.fr>" [unknown]
#> gpg: WARNING: This key is not certified with a trusted signature!
#> gpg:          There is no indication that the signature belongs to the owner.
#> Primary key fingerprint: 3CE4 6455 8A84 FDC6 9DB4  0CFB 090B 1199 3D9A EBB5
```
{% end %}

The image has been verified successfully, and it is now possible to [copy it to the USB stick](#copy-image-to-usb-stick).

### Download an alternative image

If the official image doesn't suit you for compatibility reasons, you can try to use the Nonguix one from <https://gitlab.com/nonguix/nonguix/-/releases>.

It is built by the team (or the one) maintaining Nonguix, and you'll end up using this repository for any non-free software anyway.

Just pick up the last `nonguix-system-install` version for your architecture (only `x86_64` is supported) with the associated signature file (same name with `.asc` added).
At the time of writing, it is:
* <https://substitutes.nonguix.org/nonguix-system-install-1.4.0.x86_64-linux.iso>
* <https://substitutes.nonguix.org/nonguix-system-install-1.4.0.x86_64-linux.iso.asc>

Once image and signature files are downloaded, you can try to verify the signature before importing the key:
{% wide_container() %}
```bash
gpg --verify 'nonguix-system-install-1.4.0.x86_64-linux.iso.asc'
#> gpg: assuming signed data in 'nonguix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Wed Dec 21 00:24:38 2022 CET
#> gpg:                using RSA key 81416036E81A5CF78F801071ECFC83988B4E4B9F
#> gpg: Can't check signature: No public key
```
{% end %}

Let's download the key as indicated in the [Nonguix release page](https://gitlab.com/nonguix/nonguix/-/releases/v1.4.0):
{% wide_container() %}
```bash
wget 'https://gitlab.com/jonsger.gpg' -O 'nonguix-system-install-key.gpg'
gpg --show-keys 'nonguix-system-install-key.gpg'
#> pub   rsa2048 2014-01-31 [SCA] [expires: 2025-01-28]
#>       81416036E81A5CF78F801071ECFC83988B4E4B9F
#> uid                      Jonathan Brielmaier <j.brielmaier@pantherx.org>
#> uid                      Jonathan Brielmaier <jbrielmaier@opensuse.org>
#> uid                      jonsger <jonathan.brielmaier@web.de>
#> sub   rsa2048 2014-01-31 [E] [expires: 2025-01-28]
```
{% end %}

Key fingerprint is the same, so this is the key which signed the image.

The key claims to be owned by Jonathan Brielmaier, but there is no way to confirm it through the documentation.

Searching `Jonathan Brielmaier` didn't provide any insightful result, but searching `Jonathan Brielmaier gpg` should lead you to his page on Savanah: <https://savannah.gnu.org/users/jonsger>.

This page allows downloading his GPG key, which matches the key just above:
{% wide_container() %}
```bash
wget 'https://savannah.gnu.org/people/viewgpg.php?user_id=223505' -O 'nonguix-system-install-key2.gpg'
gpg --show-keys 'nonguix-system-install-key2.gpg'
#> pub   rsa2048 2014-01-31 [SCA] [expired: 2024-01-29]
#>       81416036E81A5CF78F801071ECFC83988B4E4B9F
#> uid                      Jonathan Brielmaier <jbrielmaier@opensuse.org>
#> uid                      jonsger <jonathan.brielmaier@web.de>
#> uid                      Jonathan Brielmaier <j.brielmaier@pantherx.org>
#> sub   rsa2048 2014-01-31 [E] [expired: 2024-01-29]
```
{% end %}

Having two different sources pointing to the same key, I assume I can trust the key and so import it:
{% wide_container() %}
```bash
gpg --import 'nonguix-system-install-key.gpg' 
#> gpg: key ECFC83988B4E4B9F: 2 signatures not checked due to missing keys
#> gpg: key ECFC83988B4E4B9F: public key "Jonathan Brielmaier <j.brielmaier@pantherx.org>" imported
#> gpg: Total number processed: 1
#> gpg:               imported: 1
#> gpg: no ultimately trusted keys found
```
{% end %}

Once the key is imported, we can now verify that the signature is valid:
{% wide_container() %}
```bash
gpg --verify 'nonguix-system-install-1.4.0.x86_64-linux.iso.asc'
#> gpg: assuming signed data in 'nonguix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Wed Dec 21 00:24:38 2022 CET
#> gpg:                using RSA key 81416036E81A5CF78F801071ECFC83988B4E4B9F
#> gpg: Good signature from "Jonathan Brielmaier <j.brielmaier@pantherx.org>" [unknown]
#> gpg:                 aka "Jonathan Brielmaier <jbrielmaier@opensuse.org>" [unknown]
#> gpg:                 aka "jonsger <jonathan.brielmaier@web.de>" [unknown]
#> gpg: WARNING: This key is not certified with a trusted signature!
#> gpg:          There is no indication that the signature belongs to the owner.
#> Primary key fingerprint: 8141 6036 E81A 5CF7 8F80  1071 ECFC 8398 8B4E 4B9F
```
{% end %}

The image signature has been verified successfully, and it is now possible to [copy it to the USB stick](#copy-image-to-usb-stick).

The only issue with this image is that it is refreshed only when a new Guix System version is released (which is not so often).
As I said before, I preferred to go with the official image, so I am not sure how easy it is to install Guix System using this image and if there are impacts.

At the time of writing, there is also an important notice for the 1.4.0 release requiring to manually import Nonguix channels:

{{ image_figure(src="img/nonguix-1.4.0-notice.webp", raw_path=true, alt="The important notice regarding Nonguix 1.4.0 release", full_width=true) }}

From tests I did, I was able to use this image raw without any configuration and the Wi-Fi adapter was working in the installer.
Nevertheless, I didn't go further.

If you want to follow this path, I would advise looking at the following articles:

- <https://systemcrafters.net/craft-your-system-with-guix/full-system-install/>
- <https://wiki.systemcrafters.net/guix/nonguix-installation-guide/>
- <https://port19.xyz/tech/nonguix/>

It is worth mentioning that System Crafters provides an installation image based on nonguix and refreshed regularly.
It is hosted at <https://github.com/SystemCrafters/guix-installer>, but I didn't check myself how it is built (not sure that I am able to anyway) and the image is not signed.

Whatever the installation image you choose, the next articles of this series should apply anyway, as all rely on the same Guix System.

### Build from sources

This is a path I didn't walk through (at least not yet) but you can find useful explanations in the official documentation about [building the installation image](https://guix.gnu.org/manual/en/html_node/Building-the-Installation-Image.html).
As it requires Guix, you may need to first set up a live Guix System or install Guix on top of another Linux distribution.

Anyway, once the image has been generated, it is now possible to [copy it to the USB stick](#copy-image-to-usb-stick).

## Copy the installation image to a USB stick {#copy-image-to-usb-stick}

First, you need to know the *device name* of your USB stick.

Usually, using `lsblk` is enough to identify your USB stick with following command:
{% wide_container() %}
```bash
lsblk -o name,vendor,model,size,type,tran
#> NAME    VENDOR   MODEL       SIZE TYPE TRAN
#> sdX     SanDisk  Ultra Fit  57.3G disk usb
#> ├─sdX1                       1.4G part 
#> └─sdX2                       2.8M part 
#> xvda                          10G disk 
#> ├─xvda1                      200M part 
#> ├─xvda2                        2M part 
#> └─xvda3                      9.8G part 
#> xvdb                           2G disk 
#> xvdc                          12G disk 
#> ├─xvdc1                        1G part 
#> └─xvdc3                       11G part 
#> xvdd                       526.4M disk
```
{% end %}

The USB stick I use is a 60G SanDisk Ultra Fit, so I know its *device name* is `sdX`.
Beware yours may differ.

If it was impossible to identify the stick thanks to this command, it would be possible to use `dmesg`:
1. Unplug the USB stick
2. Run the command `sudo dmesg --follow-new` to display all the upcoming message
3. Plug the USB stick

Once plugged, you should retrieve messages similar to the following ones:
{% wide_container() %}
```bash
sudo dmesg --follow-new
#> [ <timestamp>] usb 3-2: new SuperSpeed USB device number 5 using xhci_hcd
#> [ <timestamp>] usb 3-2: New USB device found, idVendor=0781, idProduct=5583, bcdDevice= 1.00
#> [ <timestamp>] usb 3-2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
#> [ <timestamp>] usb 3-2: Product: Ultra Fit
#> [ <timestamp>] usb 3-2: Manufacturer: SanDisk
#> [ <timestamp>] usb 3-2: SerialNumber: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> [ <timestamp>] usb-storage 3-2:1.0: USB Mass Storage device detected
#> [ <timestamp>] scsi host2: usb-storage 3-2:1.0
#> [ <timestamp>] scsi 2:0:0:0: Direct-Access     SanDisk  Ultra Fit        1.00 PQ: 0 ANSI: 6
#> [ <timestamp>] scsi 2:0:0:0: Attached scsi generic sg0 type 0
#> [ <timestamp>] sd 2:0:0:0: [sdX] 120176640 512-byte logical blocks: (61.5 GB/57.3 GiB)
#> [ <timestamp>] sd 2:0:0:0: [sdX] Write Protect is off
#> [ <timestamp>] sd 2:0:0:0: [sdX] Mode Sense: 43 00 00 00
#> [ <timestamp>] sd 2:0:0:0: [sdX] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
#> [ <timestamp>]  sdX: sdX1 sdX2
#> [ <timestamp>] sd 2:0:0:0: [sdX] Attached SCSI removable disk
```
{% end %}

We can extract the *device name* `sdX` from the messages.

With the *device name*, we can issue the following command to copy the image to the USB stick:
{% wide_container() %}
```bash
sudo dd if=/path/to/guix-image.iso of=/dev/sdX bs=4M status=progress oflag=sync
```
{% end %}

Don't forget `oflag=sync` to make sure last bits are properly written to the stick (*flush*).

[^1]: <https://www.gnu.org/philosophy/free-sw.en.html>
