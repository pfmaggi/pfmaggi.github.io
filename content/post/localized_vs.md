---
title: Rhodes 5.0.2 and Visual Studio 2008 Localized edition
date: 2014-08-22T13:01:48+02:00
author: Pietro F. Maggi
summary: Obsolete - Quick post on the Joy of localized Visual Studio.
keywords: [RhoMobile, Visual Studio]
tags: [RhoMobile, Visual Studio]
topics: [RhoMobile]
type: post
---

# **2019 Update**

**The information in this post are obsolete. Please refer to the official RhoMobile documentation for best practices and setup guide.**

Oh the joy of a new RhoMobile version!

Creating new virtual machines and finally deciding that is time to leave the old trusty Windows XP and jump on the new Windows 7 with 63+1 bits. Oh man! what a pleasant use of time :-)

On top of this I wanted to spin the setup a bit and decided to use a localized version of Visual Studio 2008... what can go wrong with it?

Well... you may encounter a fancy Microsoft "feature" with a message that more or less reads:
["VCProjectEngine.dll" Could not be loaded](http://social.msdn.microsoft.com/Forums/vstudio/en-US/14dc118f-5adc-4a90-9c07-fde701f6b36c/vcbld0001-vcprojectenginedll-could-not-be-loaded?forum=vcgeneral)

This obviously, in your language of choice.

*The Solution?*

From the same thread, copy the content of the localized package for vcbuild in the main folder; in my case:

 - From: `C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\vcpackages\1040`
 - To: `C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\vcpackages`

I just made a copy of the original content before moving the files (you never know).


Enjoy your localized Visual Studio!
