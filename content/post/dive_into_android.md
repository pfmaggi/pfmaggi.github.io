---
title: Diving into Android source code
summary: Why Not? One of the beauty working on an open source OS is to be able to look into the source code to understand how it works. Don't trust the documentation (or the comments in the source code). Trust only the code itself! 
date: 2014-01-02T14:34:27+01:00
author: Pietro F. Maggi
keywords: [Android, Open Source, Hacking]
tags: [Android, Open Source, Hacking]
topics: [Android]
draft: false
type: post
---

## Why dive into Android code?
When you've to write applications for Android, you're targeting a giant moving API surface, sometimes you can be surprised of some different behavior in different Android versions or with an API call that is not well documented, so having the option to delve into Android Source Code is a great opportunity.

A lot of things can be written on how to do this, there's some documentation available on [AOSP website](http://source.android.com/source/index.html), however I think that the best starting point is a video tutorial by NewCircle's Instructor Dave Smith on this very topic:
#### Diving into Android Source Code
{{< youtube NsqFOSzoYE8 >}}

So, take your time to setup your own copy of the Android source code and, next time you're asking yourself "What's going on when you call an API, you can check the source code!"

## On a similar topic: Dalvik VM
Dan Bernstein has presented at Google I/O 2008 a talk about the internals of Dalvik VM:
#### Dalvik Virtual Machine Internals
{{< youtube ptjedOZEXPM >}}

If you're interested in this piece of the Android Source Code, this is the best starting point, even if nowadays ART has taken the place of Dalvik. A lot of Dalvik design is still in use nowadays!
