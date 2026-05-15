+++
title = "Hardware compatibility with Free software"
description = "How to know how much the hardware we have or want is compatible with Free software"

[taxonomies]
tags = ["philosophy", "os"]
+++

Whether it is to better know the hardware or to prepare the move to other operating systems (e.g. Guix System[^1]), it can be useful to know how much our hardware is compatible with *Free* software.

# What is *Free* software?

*Free* software has a clear definition by the GNU[^2]:
> “Free software” means software that respects users' freedom and community.
> **Roughly, it means that the users have the freedom to run, copy, distribute, study, change and improve the software.**
>
> -- <https://www.gnu.org/philosophy/free-sw.en.html>

You need to understand *Free* as in "free speech" (i.e. you can do whatever you want with it) and not as in "free beer" (i.e. you don't pay for it).

*Free* software must not be confused with Open Source that doesn't follow exactly the same philosophy[^3].

Whether we want to strictly adhere to *Free* software, just try to tend to it or even completely ignore it is a lifestyle choice, just like any other in other areas of life.
In any case, it's a decision that requires dedication and, like any other lifestyle choice, it deserves respect.

If there is more and more *Free* software in all the different layers of IT, there is still an indispensable one that is lacking: drivers.

A driver is a software component that lets an operating system and a device communicate.
It is a low-level piece of code closely related to the device and usually produced by its manufacturer.
If we tend to see more and more *Free* drivers, it is impossible for the community to support all the existing devices.

I know compatible drivers exist for my hardware components because my laptop has been running Linux for a while with no issue.
They might not be *Free* though.

The question now is to know how much the hardware components we have are compatible with *Free* drivers.

# Would my laptop work with *Free* software only?

I currently use an ASUS Notebook UX330U with a Core i7 7500 as CPU, 8GB of RAM and about 500GB of SSD storage.

We can [search h-node](https://www.h-node.org/search/form/en) to check how much it is compatible with *Free* software.

{{ image_figure(src="img/h-node-search.en.webp", raw_path=true, alt="The h-node search page", full_width=true) }}

`h-node` is a hardware database used to identify devices able to work with a fully *Free* operating system.
It relies on the community to feed the database so don't hesitate to contribute.

Using the search capabilities is pretty straightforward:
- Just copy and paste the output of `lspci -vmmnn` in the *"analyze the output of the lspci command"* (as explained on the page) and it will list what has been identified and if it is compatible.
You can still click on the model name to get more details.
- Just copy and paste the `<vendorid>:<productid>` from the output of `lsusb` in the *"search by vendorid:productid"* and it will let you know if it is in the h-node database.
Clicking on the model name gives you the details and if it is compatible.

Doing so, you can verify if the most important pieces of hardware of your laptop are supported: Wi-Fi adapter, USB controller, Bluetooth interface, Webcam, Fingerprint reader, ...

I can see the Wi-Fi adapter of my laptop is not compatible with *Free* software:

{{ image_figure(src="img/h-node-lspci-search-result.en.webp", raw_path=true, alt="The h-node lspci search result showing that my Wi-Fi adapter is not free-compatible", full_width=true) }}

Nevertheless, I have a USB Ethernet adapter that is compatible:

{{ image_figure(src="img/h-node-lsusb-search-result.en.webp", raw_path=true, alt="The h-node lsusb search result showing that my USB Ethernet adapter is free-compatible", full_width=true) }}

As we can see, we should think about *Free* software when we buy new hardware.
As the situation keeps evolving every day, there is no magic way on how to proceed.

If you're looking for a new laptop or hardware, the [Respect Your Freedom program](https://ryf.fsf.org/) is worth considering.
It might be a good starting point, even if the laptop list is rather limited for now.
The vendors' list can be used to browse more products and jump to other similar vendors.

[Minifree Ltd](<https://minifree.org/>), not listed there at the time of writing, is worth mentioning even if I never used any of their products myself.
Some companies, like Purism (<https://puri.sm/>), have *Free* software at the heart of their business model.
It is just an example because I am not a customer either and there must be others.

Another way can be to start from the list of *Free* operating systems provided by the GNU: <https://www.gnu.org/distros/free-distros.en.html>.
From there, it is possible to browse the Internet with your favorite search engine and look for recent people's feedback about the operating system you're interested in and the hardware it has been installed on.

[^1]: Guix strictly adheres to the "Free Software Definition".  
You can read more about its [limitations](https://guix.gnu.org/manual/en/html_node/Limitations.html) and [hardware considerations](https://guix.gnu.org/manual/en/html_node/Hardware-Considerations.html).
[^2]: <https://www.gnu.org/gnu/gnu.en.html>
[^3]: <https://www.gnu.org/philosophy/open-source-misses-the-point.en.html>
