---
title: Tips on how to demo Android devices
summary: My work requires to demo a feature or a particular application on a mobile device while showing the actual device screen on a big screen. These are some of the tools I use and some of the tricks I like to use.
date: 2017-10-13T17:20:00+01:00
tags: [Android, Tips&Tricks, Zebra Technologies]
draft: false
author: Pietro F. Maggi
---

Have you ever presented a new Android device to a tech crowd trying to explain some feature?

it happens to me quite a lot and over time I built my little bag of tricks on how to do it.

## 1. Use Vysor to show my device on the screen
[Vysor](https://www.vysor.io/) is my go-to solution for showing my device screen on the PC during demo.
The only requirement in this case is to have adb enabled on your device and having a working adb on your PC/mac. Everything else is handled by Vysor!

![Vysor homepage](/images/20171013_demo_android/vysor.png "Vysor homepage")

Is available with advertisement and [very cheap to buy a monthly or annual subscription](https://www.vysor.io/#pricing).
I don't know why but, even if Paypal is listed the available payment methods, it didn't work for me...

## 2. Enable Show Touches in Android's Developer Options
This is an "advanced" trick.

Simply enabling this option allows the people looking at your display to understand where you're touching the screen.

![Show touches](/images/20171013_demo_android/settings.png "Show touches")

...Remember that the image shown on the screen does not show your fingers and where you're touching :-)

## Final tip
The same setup can be used to record your screen to build a small screencast.

In this case, macOS and Quicktime are two tools that make this really easy!

 {{< youtube 16YkEJvmNzg >}}
