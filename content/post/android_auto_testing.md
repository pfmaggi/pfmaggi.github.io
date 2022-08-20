---
title: Android Auto testing
summary: I got a new car this summer that supports Android Auto, some apps I use on my phone works out of the box, others required some work. I remembered a couple of years ago Google presenting a tool to test AndroidAuto apps... it ends up that there's "space for improvements" in the  available documentation.
date: 2017-10-31T21:00:00+01:00
tags: [Android, AndroidAuto, Testing]
draft: false
author: Pietro F. Maggi
---

## Android Auto? Why?
This summer I bought a new car and it came with a nice 8'' touch panel that supports Android Auto and Apple Car.

It even came with seven seats and a nice engine, and that was way more important than having an additional screen... but, it's there, and I started to make good use of it.

Playing with Android Auto is a bit of a hit and miss. At least for me! I never looked into it, but it works well for the basic features, but I didn't understand why it's not supporting [AntennaPod](https://github.com/AntennaPod/AntennaPod). This is my app of choice to listen to podcasts, something that I'd like to do when I commute to the office or when I drive alone.

Looking into AntennaPod repository it looks like that it supports Android Auto, but nothing is coming up on the car while other audio apps "just works".

I needed to understand what was going on.

## Some investigation
To use Android Auto, first, you need to install
[Android Auto Companion App](https://play.google.com/store/apps/details?id=com.google.android.projection.gearhead) on your mobile.

Then, connecting the mobile to the car through USB, you get the supported apps on the 8'' screen.

The development pieces are explained on [Android Auto developer website](https://developer.android.com/training/auto/index.html), we're supposed to find there all the information...

Given that AntennaPod is opensource, I started to investigate if all the required pieces were in place and recompiled the app from sources. However having to go to the car to test the build didn't look like a great idea.

For sure there should be a better way to test it!

## Need for testing
Luckily (or more probably, on purpose) Google released in 2015 [Android Auto Desktop Head Unit](https://android-developers.googleblog.com/2015/08/announcing-android-auto-desktop-head.html). A desktop application that can be used to test Android Auto behaviour of an Android app.

![Android Auto Desktop Head Unit](/images/android_auto/desktop_head_unit.png "Android Auto Desktop Head Unit")

All good if only the documentation was correct.

Let me explain, to use the Desktop Head Unit, you need to enable developer mode on the Companion App (on your android device), but the documentation is probably out of sync with the current version of the Companion App.

At least, for me, the first step of the process to enable developer mode was non-sense:

> On the mobile device, enable Android Auto developer mode by starting the Android Auto companion app and then tapping the Android Auto toolbar title 10 times. This step is only required the first time you run the companion app.

So, after having tapped everywhere on the app, I went back to the source of all truth, **StackOverflow**, and found this question: [Android auto - how to enable developer mode](https://stackoverflow.com/questions/33578041/android-auto-how-to-enable-developer-mode).

Here you can find some reference to the documentation and then, down in the thread, someone suggesting the right steps:

To enable Android Auto developer mode:

1. Start the [Android Auto companion app](https://play.google.com/store/apps/details?id=com.google.android.projection.gearhead&hl=en)
2. Open Navigation Drawer
3. **Tap About menu item**
4. **Tap 10 times the top horizontal Toolbar where it says "About Android Auto"**
5. A Toast message shows up when the developer mode has been activated
6. Continue with the next steps - [Start head unit server from the menu](https://developer.android.com/training/auto/testing/index.html#running-dhu), etc.

And this works! at least with the current version of the companion app.

## Comments
My initial idea with Android Auto was that I could have controlled the screen on my car from the mobile phone app. In reality, and probably not a bad idea, what is possible for an application is somewhat limited for security reason.  However, I'm now happily listening to my podcasts while commuting being able to control AntennaPod from my car interface... if only it was possible to change the replay speed... maybe next time :-)

## References

* [Android Auto companion app](https://play.google.com/store/apps/details?id=com.google.android.projection.gearhead&hl=en)
* [Announcing Android Auto Desktop Head Unit](https://android-developers.googleblog.com/2015/08/announcing-android-auto-desktop-head.html)
* [Android Auto developer website](https://developer.android.com/training/auto/index.html)
* [AntennaPod](https://github.com/AntennaPod/AntennaPod)
