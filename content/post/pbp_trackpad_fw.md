---
title: Updated Trackpad firmware for Pinebook Pro
summary: The beauty of the Pinebook Pro is that it is a tinkering machine. This article covers briefly an update made available by the community for the trackpad of this machine.
date: 2021-08-06T20:00:00+02:00
tags: [Linux, PinebookPro, firmware]
draft: false
author: Pietro F. Maggi
---


## Having the right expectations

The Pinebook Pro (PBP in the rest of the article) is a tinkering machine, an impressive one; not in term of raw power but for its hackability. It has some sharp edges. It's a cheap machine and I never had much expectation for its keyboard and trackpad. Nevertheless, I'm typing this on the PBP's keyboard and it's not that bad.  
I have more problems with the trackpad, in particular with its palm rejection (or lack of it). It happens that it is triggered and move my cursor while I'm typing. My standard solution is to use an external mouse and disable the internal trackpad. Using a tiling manager and working mostly in the terminal also helps. But there's always the need to use a browser and there a pointing device comes really handy.

So, when I read about a new improved firmware for the trackpad, I was in to test it!

## Forum and PBP community

[Finally... The touchpad works great!](https://forum.pine64.org/showthread.php?tid=14531) is the forum announcement about this new fw version and it links to the [repository](https://github.com/dragan-simic/pinebook-pro-keyboard-updater) with updated fw, the utilities, and the instruction on how to update a PBP.  
Here goes the standard disclaimer regarding the firmware of a piece of hardware: there's always the possibility that something goes wrong. As I stated since the beginning, the whole point of the PBP is that it's a tinkering machine. I assume that you know that with computers, if something can go wrong, it will find a way to go wrong.

## Keyboard controller IC

There are notes in the discussion regarding different keyboard controller installed in the PBP and it seems that you may have one of these ICs:

- the "full-fat" version, [SH68F83](https://github.com/dragan-simic/pinebook-pro-keyboard-updater/blob/master/firmware/docs/sinowealth-sh68f83-datasheet-v2.pdf), and that it supports many write cycles
- the "little" version, [SH61F83](https://github.com/dragan-simic/pinebook-pro-keyboard-updater/blob/master/firmware/docs/sinowealth-sh61f83-datasheet-v2.pdf), which is limited to a total of eight writes

I tried to understand how to identify which IC is installed on my PBP but it seems that the only option is to open the machine and visually check which IC is installed. [Here's a Reddit discussion on this topic](https://old.reddit.com/r/PINE64official/comments/loq4db/very_disappointed/).

Given that this is the first time that I flash the firmware, I went ahead without checking. I added this to my list of things to check the next time I'll open my PBP.

## Update and feedback

The process, for me at least, was smooth and without issues. It is important that you have handy an external keyboard during the process. This is required if you have an ANSI machine (with the US layout) but it is a good idea to have an external keyboard handy if something goes sideways.

So, how is the trackpad with the new firmware?

It looks indeed more precise and responsive and a clear improvement over the previous one but I still have problems with it picking up randomly my palm while I use the keyboard... so it's better but it doesn't completely solve my problem.

Let me know if you find
