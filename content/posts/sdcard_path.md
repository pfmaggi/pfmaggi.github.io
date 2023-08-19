---
title: Finding the SDCard Path on Android devices
date: 2014-10-19
author: Pietro F. Maggi
summary: Obsolete - Finding the real SDCard path in Android v4.1 JellyBean can be tricky. Let see how we can do it on a TC55. 
tags:
    - "Android"
    - "Zebra Technologies"
    - "Sample"
    - "SDCard"
---


{{< div class="danger pure-button" >}}
> The information in this article is obsolete.<br>Please refer to the official RhoMobile for best practices and setup guide
{{<div "end" >}}

With Motorola Solutions we're busy finishing the first round of Android Developer's Kitchens around Europe. Just in time to prepare for the [Enterprise App Forum in Brussels!](http://www.motorolasolutionsevents.com/enterprise_appforum_2014/).

The nice things of these events is the interaction with our partners and theirs real live problem!

Last week I got an interesting question regarding our TC55:

**How can I programmatically get the path for the SDCard on the TC55?**

That's a good question; usually you don't want to insert in your code the dependency to an hard coded path!
The usual answer is based around the function `Environment.getExternalStorageDirectory();`.

The problem is that this function report a different folder if the SDCard is inserted in the device or not.
This is not a problem unique to the TC55, it's a common problem on Android and there are different ways to handle it:

  - [Reading the partition table from <code>/proc/mounts</code>](http://futurewithdreams.blogspot.it/2014/01/get-external-sdcard-location-in-android.html)
  - [Use some environment variables](http://stackoverflow.com/a/23949650/118862)
  - [Still use the getExternalStorageDirectory() function, together with the isExternalStorageRemovable() and getExternalStorageState() functions](http://stackoverflow.com/questions/22219312/android-open-external-storage-directorysdcard-for-storing-file)

Given that we've the luxury to target just a few devices, the Motorola Solutions one :-), the best option is to use the environment variables.

### Let's see what happens on the TC55
On this device, using <code>Environment.getExternalStorageDirectory();</code> returns different results depending if you've installed or not an SDCard:

 - Without SDCard —> `/STORAGE/SDCARD1`
 - With SDCard —> `/STORAGE/SDCARD0`

Let's see what we can achieve with the environment variables!

We can log into a TC55 and use the <code>printenv</code> command:

```bash
adb shell
printenv

_=/system/bin/printenv
LD_LIBRARY_PATH=/vendor/lib:/system/lib
HOSTNAME=android
BOOTCLASSPATH=/system/framework/core.jar:/system/framework/core-junit.jar:/system/framework/bouncycastle.jar:/system/framework/ext.jar:/system/framework/framework.jar:/system/framework/framework_ext.jar:/system/framework/android.policy.jar:/system/framework/services.jar:/system/framework/apache-xml.jar
PATH=/sbin:/vendor/bin:/system/sbin:/system/bin:/system/xbin
LOOP_MOUNTPOINT=/mnt/obb
ANDROID_DATA=/data
ANDROID_ROOT=/system
SHELL=/system/bin/sh
MKSH=/system/bin/sh
USER=shell
EXTERNAL_SDCARD_STORAGE=/storage/sdcard0
ANDROID_PROPERTY_WORKSPACE=8,49152
EXTERNAL_STORAGE=/storage/sdcard1
ANDROID_ASSETS=/system/app
TERM=vt100
RANDOM=16913
ASEC_MOUNTPOINT=/mnt/asec
SECURE_STORAGE_SDCARD=/storage/sdcard0
HOME=/data
ANDROID_BOOTLOGO=1
MASS_STORAGE=/mnt/udisk
PS1=$(precmd)$USER@$HOSTNAME:${PWD:-?} $
```

From this is easy to see that the way to get the SDCard path is to use a couple of environment variables:

 - `System.getenv("EXTERNAL_STORAGE")` —> Internal SDCard `/STORAGE/SDCARD1`
 - `System.getenv("EXTERNAL\_SDCARD\_STORAGE")` —> True SDCard `/STORAGE/SDCARD0`

This solutions is still depending on two specific environment variables, is much better than hardcoding the path strings in the application but is probably only acceptable if you're targeting just few devices.
Another point is that, on other devices, you get the true SDCard path linked to the `SECONDARY_STORAGE` environment variable. So a better solution could be to check for both variables with something like:

```java
String strSDCardPath = System.getenv("SECONDARY_STORAGE");
if ((null == strSDCardPath) || (strSDCardPath.length() == 0)) {
    strSDCardPath = System.getenv("EXTERNAL_SDCARD_STORAGE");
}
```

When we will start to release KitKat based devices it will be even more important taking a look at the right path for these volumes :-)

{{< div class="pure-button" >}}
> I've a new [blog post]({{% relref "posts/android_secondary_storage_1.md" %}}) with additional information for Zebra Technologies KitKat devices.
{{<div "end" >}}

You can find in my github account a [sample application](https://github.com/pfmaggi/GetDeviceInfo) that retrieves these data.

Let me know if you find this information useful via [twitter @pfmaggi](http://twitter.com/pfmaggi) or [email](mailto:pfm@pietromaggi.com) or [joining me and the EMEA Software Enablement Team in Brussels for the Enterprise AppForum, our annual developer events](http://www.motorolasolutionsevents.com/enterprise_appforum_2014/).
