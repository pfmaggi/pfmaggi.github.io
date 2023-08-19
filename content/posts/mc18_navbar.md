---
title: Toggling MC18 Navigation (and Status) Bar visible state
summary: On Zebra MC18 with Android, it's possible to toggle the NavBar visible status with a XML configuration file...
date: 2016-10-30T11:00:00+01:00
tags: [Android, MC18, Zebra Technologies]
draft: false
author: Pietro F. Maggi
---


MC18 is the only Zebra Android mobile computer (at least till now) that uses a Navigation bar instead of phisycal buttons for the back and home.
This is the first device where it makes sense to use [Android immersive mode](https://developer.android.com/samples/ImmersiveMode/index.html)!

The problem with the standard immersive mode is that swiping from the bottom or the top of the screen re-enable both Navigation and Status bar. It's not really intended to be a kiosk mode... is just to have a full screen capability.

Luckily, MC18 Android supports natively a setup to hide both Status and Navigation bar making much more easy to lock a user inside a single application.

## Enter the `navbarconfig.xml`
Android version of the MC18 has a configuration xml file to control the navbar visibility status located in the `/enterprise` partition:

    /enterprise/device/settings/navbarconfig/navbarconfig.xml

This file can be used to configure permanently the visibility status or to change it dynamically (this latest option is supported starting from BSP v02-13-12).

To change it permanently, set the content of the file to:

    <?xml version="1.0" encoding="utf-8"?>
    <navbarconfig>
        <hidenavbar>false</hidenavbar>
    </navbarconfig>

You can copy the file created in this way to the device using adb or an MDM, but you cannot navigate to `/enterprise` due to some permission restrictions. However you can navigate directly to the `/enterprise/device/` folder (and the folders under it).  So, can use adb to push the data on the device:

    adb push navbarconfig.xml /enterprise/device/settings/navbarconfig/navbarconfig.xml

Then you need to reset the device to activare the new setting.

If you've the BSP v0-13-12 or newer you can change the setting dynamically, without the need of restarting the device, you can use this xml file inside your `navbarconfig.xml`:

    <?xml version="1.0" encoding="utf-8"?>
    <navbarconfig>
        <hidenavbarinstant>false</hidenavbarinstant>
    </navbarconfig>

Once you push this file to the device, it will modify immediately the configuration.
If you've an older BSP [you can download v02-13-12 from our support website](https://portal.motorolasolutions.com/Support/XU-EN/Resolution?solutionId=101574&productDetailGUID=a3b89f5683686410VgnVCM10000001c7b00aRCRD&detailChannelGUID=96d05645aa79f310VgnVCM10000081c7b10aRCRD) if you have a valid maintenance contract or you're registered as an ISV partner.

As an alternative you can toggle the navbar status using a broadcast intent with action `com.symbol.intent.action.systembarvisibility`, with a boolean extra with name  `visibility` as shown below in the code snippets:

    private final static String mStrIntent = "com.symbol.intent.action.systembarvisibility";
    private final static String INTENT_EXTRA = "visibility";

    Intent i = new Intent(mStrIntent);
    i.putExtra(INTENT_EXTRA, mbNavBarVisible);
    sendBroadcast(i);

This allows your application to hide/show the Navigation bar at will.

Nice trick :-)

*Thanks to Kelly, Gary, Andy and Kjell for providing the info needed to put this together!!!*


**NOTE:** The Intent to control the visibility status is not currently documented and, even if it works on the current version of the Operative System, it is not guarantee to work on future OS releases.

If you want a future proof control of the NavBar visibility, you can copy the updated configuration file from your app to the `/enterprise/device/settings/navbarconfig/` folder.
