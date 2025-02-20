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

Installation can be summarised in 3 steps:

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

############ TODO
How to configure password

One more step toward fully protecting the laptop from tampering with the boot sequence is to enable UEFI Secure Boot.
Unfortunately, Guix System doesn't support secure boot yet [ref] even if there is some work in progress [ref].
Anyway, it is better to activate it later to not overcomplexify the first installation.
I'll add a dedicated article to this series if I find a way to set it up properly.

############ TODO
How to configure boot order
############ TODO
How to temporary boot on the key

## Boot on the USB stick and configure basics

When booting on the USB stick, you arrive on Guix System graphical installer by default.
It is available on *TTY1*.

*TTY2* provides access to offline Guix documentation and *TTY3*, *TTY4*, *TTY5* and *TTY6* let you access root shells for manual installation.

Switching to a specific TTY is achieved by hitting `ctrl-alt-f<TTY#>`, `f<TTY#>` being one of the function keys `F1` to `F6`.

First of all, let's swtich to *TTY3* (`ctrl-alt-f3`) and make sure the keyboard will behave as we expect it to.
Default is QWERTY.
If you need to change it to something else, just use following commands when on *TTY3* to *TTY6*:
{% wide_container() %}
```bash
# Check what is the current configuration
localectl status
#> TODO

# List available keymaps if needed
localectl list-keymaps
#> Output is too long and useless to be pasted here

# Change your keyboard keymap
## For AZERTY
sudo loadkeys fr
# For BEPO
sudo loadkeys fr-bepo
## ...
## To come back to QWERTY (which is the default)
sudo loadkeys us
```
{% end %}
{% admonition(type="tip", title="Setting up keyboard layout") %}
`loadkeys` command update the keymap of the current terminal session.  
Please note this may not persist it through reboot nor update the configuration of other sessions.  
In our case, we don't care about reboot but it means you may have to enter this command for all *TTY*s.
{% end %}

############ TODO
Confirm it works as expected:
 - does loadkeys persist through all tty?
 - loadkeys in sudo or not?
 - localectl status update?


############ TODO
Newtork

## Partition

## Configure everything needed and install

# First boot

Upgrade channel


[^1]: <https://en.wikipedia.org/wiki/BIOS>
[^2]: <https://en.wikipedia.org/wiki/UEFI>
[^3]: <https://doc.coreboot.org/distributions.html>
[^4]: You can look at <https://en.wikipedia.org/wiki/Evil_maid_attack> for a description of the attack and at <http://theinvisiblethings.blogspot.com/2009/10/evil-maid-goes-after-truecrypt.html> for a practical implementation example
 
