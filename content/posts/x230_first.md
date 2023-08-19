---
title: Thinkpad X230 - First linux setup
description: A first for me, finally having time to sit down for a few evenings and setup my Thinkpad X230 with linux. This is supposed to be my thinkering machine for the coming months.
date: '2023-08-15'
author: Pietro F. Maggi
showAuthor: false
draft: false
tags: [ Linux, Personal, X230 ]
meta: true
math: false
toc: false
hideDate: false
hideReadTime: false
---

I bought this refurbished machine for me at the end of 2019 on ebay, then the pandemic hit and we needed to setup everybody with a computer for home schooling. It worked great with the preinstalled Windows 10 and my son loved it. Its main issue?

Battery life

Not a problem when you use it at home, but when school reopened, my son had to ask for a desk close to a power socket. Not exactly the best experience.
So, at the end of 2021, I bought a new AMD based Chromebook for him (amazing battery life and [Google's official support till June 2029][1]) and the machine was back to be mine for thinkering.

So I started with my plan, just a few weeks to have the machine setup!

> *Narrator: he was not great at planning*

## Upgrades

As I wrote, the machine had a functioning battery, but battery life was less than one hour by the time I got the machine back from my son. Also I wanted to max out the RAM to 16GB. I may want to do some light Android development on it and the more installed memory I could have, the better.

### Battery and BIOS updates

I went back to ebay and bought a new battery and the RAM I needed.

> *Narrator: even the best plan start to fall apart when it hits reality*

Lenovo Thinkpads have a BIOS check to verify that the battery is an original one. To avoid dangerous knockoff... obviously the battery I bought was seen as not original and it was not charging... So my plan got a bit more complex.

Searching on line I was glad to discover that I was not the only one with this issue and that BIOS modifications were available to fix the issue. A detour on [1vyrain's gitub account][2] and a custom BIOS fixed the issue for me.

Now my X230 was happy with a new 6 cells battery and 16GB of RAM.

## Choosing an OS

I wanted to keep the existing Windows partition (just in case my son needed again the machine as a backup), but I also wanted to move my stuffs under linux. This means having grub installed, not a problem there. Bigger problem was about choosing a distribution.

I wanted to try [Pop!_OS by System76][3], but after a while I moved to [Manjaro][4] with Sway as WM.

## Software

Next step has been to setup the machine and install some software starting with emacs with [doom-emacs][5].

With emacs in place I could start setup the rest. These are some of the tools I have now for my tinkering:

- Firefox
- fish is my shell and foot is my terminal
- rustup for rust
- zig for the fun of playing with a new language
- asdf together with Erlang and Elixir to do some work with the [Nerves Project][6]
- Calibre is the trusted companion for my Kobo Libra 2

[1]: https://support.google.com/chrome/a/answer/6220366?hl=en#zippy=%2Chp
[2]: https://github.com/n4ru/1vyrain/
[3]: https://pop.system76.com/
[4]: https://manjaro.org/
[5]: https://github.com/doomemacs/doomemacs
[6]: https://nerves-project.org/
