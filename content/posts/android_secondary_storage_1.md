---
title: Secondary External Storage in Android KitKat - Part 1
summary: Obsolete - New rules control Android v4.4 Secondary External Storage, here's what you need to know.
date: 2016-01-14T23:00:00+01:00
tags: [Android, SDCard, Zebra Technologies]
topics: [Android]
draft: false
author: Pietro F. Maggi
type: post
---

# **2019 Update**

**The information in this post are obsolete. Please refer to the Android documentation for updates on this topic.**

First technical post for 2016. Finally!

To kick-start 2016 I've chosen a topic that is more and more relevant in our market: The changes introduced by Google in Android v4.4 KitKat, regarding the secondary storage (the SDCard).  
I've already talked about this in my AppForum 2015 talk last October in London[^1], but I want to add some extra info and rationales here.

## A bit of background
Android's purpose is to establish an open platform for developers to build innovative apps.
For this reason, Google put in place a compatibility program that defines technical details of the Android Platform;

For every Android release, there's a Compatibility Definition Document, the last available at this moment is for [Android 6.0, aka Marshmallow](http://static.googleusercontent.com/media/source.android.com/it//compatibility/6.0/android-6.0-cdd.pdf), that defines some of the technical features and device needs to have to be a compatible Android device. And this compatibility is a requirement to be able to license GMS[^2] for the device.

To put it clearly:

> You want GMS? You need to comply to the CDD

Here we're talking about Android v4.4 KitKat, so here's a link to [Android 4.4 CDD](http://source.android.com/compatibility/4.4/android-4.4-cdd.pdf)

## What's the catch?
From the CDD, Android devices are required to have a Secondary Storage available with a minimum 1GB size if you don't have this internal in your device but you provide an SDCard slot, you need to ship the device with an SDCard, again, 1GB minimum size.  
So usually, you end up that the SDCard is your *Secondary External Storage*, and the *Primary External Storage* is the 1GB partition (minimum) you included in the device.

> Not a big deal

To access an External Storage on Android v4.3 and previous versions, you have to specify a couple of permissions in your `AndroidManifest.xml`:

- `READ_EXTERNAL_STORAGE`
- `WRITE_EXTERNAL_STORAGE`

These will gave you read&write access to all the External Storage available[^3].  
Given that there are ways to gently ask for the [SDCard true path]({{ relref . "post/sdcard_path.md" }}) this was more or less working.

> But Android is now a multiuser OS! and an SDCard with a FAT file system, is not very good protecting the data between the different users...
  
Android support SDCard with FAT file system, to provide a layer of security, it uses the [Linux FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace).

In Android v4.4 Google decides to go a step further mandating that an app can write (with the right permissions that we just saw) only on the Primary External Storage. For all the other External Storage, an app can only write in its package folder. e.g. if you app package is com.pietromaggi.sample.externalstorage, It can only write in the folders `<sdcard root>/Android/data/com.pietromaggi.sample.externalstorage`.  
There's no way, for a normal application, to write anywhere else on the SDCard.

## OK, but there's an happy end somewhere?
Really, if you're trying to get a way to revert Android behavior to what it was before API level 19, no, this is it.  
Luckily Google has introduced new API in KitKat (and Lollipop and Marshmallow) that allows to mitigate the issue. Some user interventions may be needed but at least there are standard APIs available.

In the second part of this blog I'll present a sample app that uses some of these APIs.


[^1]: Slides presented in London: 

[^2]: GMS Stands for Google Mobile Services, and is the collection of services and application built by Google that are not part of AOSP (Android Open Source Project) like GMail, Google Maps, Google Play Store and the push notification service GCM (Google Cloud Messaging).

[^3]: The catch is that, before Android v4.4, API level 19, and the introduction of the `Environment.getExternalFilesDirs()` there was no way to know how many External Storage you had available on a device. The only available API was `Environment.getExternalStorageDirectory()` that returns the Primary External Storage.
