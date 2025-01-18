+++
title = "Choosing an OS according to expectations and security threats"
description = "A quick definition of my expectations and threats regarding operating systems and the choice of the one that might best meet them, at least on paper"

[taxonomies]
tags = ["os", "guix", "nix", "security"]

[extra.series_template_variables]
position = "first"
curr_achievement = "we have chosen Guix System as a new operating system"
next_objective = "how to prepare for its installation"
+++

I plan to move to another OS in order to smooth my daily digital life which mostly consists in browsing the Internet, developing and playing around with security challenges.
In addition, it has also to limit my risks according to my threat model.

I will try to clearly describe what I expect from such a system and what kind of threats I may face so that I can ensure they are all covered as much as possible.

I would like to emphasize that it is not a complete and fair comparison of all existing operating systems.
This analysis is tailored to my personal situation, and yours may and should be different.
I would recommend using this article to help you identify your own expectations and threats, instead of as a concrete list to follow.

Last but not least, this brief comparison is based on data collected from the Internet.
At the time of writing, I did not perform any experiments, and the outcome may differ from the expectations described here.

# What do I expect?

## Great developer experience 

I spend a lot of time developing so the system must allow an excellent experience as a developer, including for mobile.
Being able to connect with an android phone is a must in order to keep mine up-to-date.

I want this system to improve my development productivity in a reliable, reproducible and sustainable way.

Last but not least, being able to test new tools, new libraries or new way of working should be made easy without putting the whole system at risk.
Being able to get rid of anything useless to me is a must-have.

## Easy to keep up-to-date

I want to be able to keep the system running until I can invest some time to properly manage an upgrade or upgrade in a granular way a part of the system without requiring to look at the entire system.

If it forces me to upgrade, install and test again everything when I don't have enough time, I will end up doing the minimal quick and dirty stuff to be able to use the system with minimal capabilities.
The more I use it in this state, the less I feel motivated to invest time to upgrade it and, I just end up thinking about how to completely replace it with something better.

## Fully configurable in a declarative way through text files

I don't like GUIs... at least for configuring my operating system.
I want to be able to write down in a text file what I expect from the system and let it configure itself to reach this desired state properly.
Advantages of text files are that they are easy to store, easy to back up, easy to share, easy to compare and their generation can be easily automated.

Cherry on the cake, if the system configuration is reproducible, it would make sure I can rebuild everything from scratch without worrying about it.

## Make software built from sources easy but only when needed

Build software from sources can really be time-consuming and become a nightmare.
Having an incremental build and relying on binaries caches is indispensable.
Self-hostable binaries cache is even better, even if it requires enough maturity to manage them (which won't be the case at first).

Nevertheless, from time to time, for an uncommon configuration or a specific tweak, building from sources can be really useful.

## As much free and open source as possible

I am fully in line with the "Free Software Definition" [^1] and I do agree that open source is not always equivalent to free.
Nevertheless, I also acknowledge that I am not able, neither in terms of time invested nor in terms of money, to avoid using any non-free software.

My current situation is that I do my best to use a free software whenever possible and at least open source software.
I may use closed-source software in places I am not confident to deal with (e.g. boot sequence, GPU driver ...).
Maybe some day, I'll feel confident enough to invest more time and resources in getting rid of any non-free software I use.

# A simple threat model

A threat is a potential or actual undesirable event that may be malicious (such a ransomware attack) or incidental (such as a computer crash).

In my case, the threat model will be contextualised to what could happen to my personal laptop, living in a place where I don't feel a risk for my life if I fail in securing it properly.

This has to be kept in mind not only while looking at this model but also while applying the configuration I'll propose as this may not suit your own situation.
Nevertheless, I'll try to move the security level above what is just require from this analysis everytime it is possible and remains usable according to my personal feeling.

## Unavailability due to laptop failure or destruction

A laptop can break down at any time, whether due to wear and tear or an accident.

As I am focused on the laptop only, I'll assume data is properly replicated elsewhere and I am able to access it without it.
I may cover data backup and recovery in the future but it is out of the scope of this series.

Anyway, if my laptop becomes impossible to use, I want it to be easily reinstalled as it was once it is repaired (or replaced).

## Data discolure due to laptop theft

A laptop is considered a valuable object and can be the target of a theft.

If it happens, I want to make sure it will be if not impossible at least very hard to get access to the data hosted in it.

## System compromise due to Internet browsing

I spend a lot of time on the Internet and I develop every time I can.
There is always a risk of compromise either by browsing directly or by downloading bad things (binary, code, dependency).

If it happens, I want the compromise to be limited to the browsing session with as few way to persist after a reboot as possible.
In addition, the compromise should be limited to a particular context (e.g. the development session) wihtout being able to easily spread to the entire system.

# Choosing my new operating system

Due to the fact I would like a declarative and reproducible way to configure my operating system, I quickly end-up with the two following ones:

- NixOS (<https://nixos.org/>)
- Guix System (<https://guix.gnu.org>)

Those operating systems are really similar.
They both rely on a declarative package manager to make system configuration reliable, reversible and reproducible:

- NixOS package manager is nix, relying on nix language
- Guix System package manager is guix, relying on Guile Scheme language

As there are already many comparisons between those two operating systems [^2], I'll just explain why I chose Guix System over NixOS.
This choice is mostly subjective because both are great operating systems.
You just sometimes need to follow your gut.

First of all, Guile Scheme looks cleaner than nix.
It is just my personal taste but as all the configuration will be done using one of those languages, it is better to feel at ease with it.

Nix has many more packages than Guix and they are more up-to-date.
Nevertheless, as most of my tooling is Rust-based, I expect it to be not so complex to compile (but I may be completely wrong).
In addition, it seems possible to use nix from Guix System which would allow taking advantage of the huge nix package repository.
The other way seems true as well, even if it might not be as interesting. 

Both of them have binary caches which would allow to pre-compile software I use.

Guix feels more unified and integrated.
This may be due to the smaller community or the slower pace of evolution, but it makes me confident I'll be able to follow and move only when I want to.
I am not sure whether it is good or bad, though.
Being unified is good if enough is covered to be useful.
I assume it is the case for Guix System.

By the way, I am a bit afraid that I may have to package too much software by myself, in particular non-free ones that my current laptop may require.

But I think I can give Guix System a try and fall back to NixOS if it becomes too time-consuming.

What's more, if NixOS is not practical as well for me, I identified CachyOS (<https://cachyos.org/>) which is based on Arch Linux and may be faster to configure.
Even if its configuration is not declarative, it seems security is a core feature alongside performance.

Before finishing, I would like to mention RedoxOS (<https://www.redox-os.org/>).
It is a completely new general-purpose operating system with a microkernel architecture under active development.
Unfortunately, it is not mature yet for production use.
If its configuration is made easy and, even better, in a declarative way (let's dream a bit), it could become a fascinating operating system to look at in the future.


[^1]: <https://www.gnu.org/philosophy/free-sw.en.html>
[^2]: I listed a few articles to start with.
      There is no particular order and it is incomplete, so just use your favorite search engine.
      - <https://forum.systemcrafters.net/t/nixos-vs-guix-a-non-programmers-novice-perspective/875>
      - <https://lwn.net/Articles/962788/>
      - <http://www.willghatch.net/blog/2020/06/27/nixos-the-good-the-bad-and-the-ugly/>
      - <https://www.reddit.com/r/NixOS/comments/1d2s6r1/why_nixos_won_over_guix/>
      - <https://news.ycombinator.com/item?id=32927469>
      - <https://www.youtube.com/watch?v=PiAMRXYIri0>
      - <https://doronbehar.com/articles/why-I-chose-NixOS/>
      - <https://gist.github.com/abcdw/e54807b0a25e61fe2cf1bf8991410f83>