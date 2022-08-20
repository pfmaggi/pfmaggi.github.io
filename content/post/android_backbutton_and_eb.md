---
title: Android Back Button and Enterprise Browser
summary: Obsolete - How to intercept the Back Button when using Zebra's Enterprise Browser on Android devices.
date: 2015-09-25 11:33:11 +0200
tags: [Android, Enterprise Browser, Tricks, Zebra Technologies]
draft: false
author: Pietro F. Maggi
type: post
---

# **2019 Update**

**This post is now obsolete. Enterprise Browser has new APIs to cover this use case, please refer to its documentation for updates.**

## Edit
Enteprise Browser v1.3 introduced a change that requires to enable `FunctionKeysCapturable` to be able to use the KeyCapture API.  
To do this you need to update your `Config.xml` with:

```
<FunctionKeysCapturable                 value="1"/>
```

## Enterprise Browser
Enterprise Browser is Zebra Technologies' Industrial Browser built for our Rugged Devices, we support most of our Windows Mobile/Windows CE devices and Android devices.

This product is usually used with existing web application that cannot be updated/modified moving from one device to another.
This help us winning some opportunities but sometimes present some unique challenges like, as an example, disabling the Android back button default behavior.

## Back Button on Android?
Given that this is a cross-platform product (built on top of our RhoMobile Suite) we don't have a custom settings to disable the back button in the Config.xml configuration file, however it is still possible to add a default Metatags in the configuration so that no changes in the existing pages are needed.

So, do we've an API to intercept the Back Button?

Of course! the [KeyCapture API](https://developer.motorolasolutions.com/docs/DOC-2520) can be used to intercept and disable it.

```
EB.KeyCapture.captureKey(BOOLEAN dispatch, STRING keyValue)
```

Setting the `dispatch` value to `false`,  We just need to get the correct KeyCode.

This can a bit tricky and can be different from device to device, the best solution I've is to ask directly to the device with a simple HTML page that display the keycode off all the keys that are pressed:

```
<!DOCTYPE html>
<html lang="en">
<META HTTP-Equiv="KeyCapture" Content="KeyValue:All; Dispatch:False; KeyEvent:url('JavaScript:alert('Key Pressed: %s');')">
  <head>
    <meta charset="utf-8">
	<title>Display KeyCode</title>
  </head>
  <body>
    <H1>Press a key to see it's KeyCode</H1>
  </body>
</html>
```

Once we've the correct KeyCode, we just need to call this API in every page that is loaded in the browser... Sometimes that is not easy to do sometimes, especially when the application cannot be modified.

We've a solution for this issue: having a `DefaultMetaTags` in Enterprise Browser's configuration.



## What's a DefaultMetaTags
We can start from the [documentation](https://developer.motorolasolutions.com/docs/DOC-2571#defaultmetatags), what does it means?

A `DefaultMetaTags` is a way to call an Enterprise Browser API on top of every HTML page that is loaded in the browser. This can be used, as an example to enable on screen UI elements like the battery or WiFi icon.

So, putting the different things together, we can put this in the ''Config.xml':

```
<DefaultMetaTags>
    <MetaTag value="KeyCapture~KeyValue:0xA6;Dispatch:False"/>
 </DefaultMetaTags>
```

BTW: `0xA6` is the correct value for the Back Button on a TC55 running Android v4.4 KitKat.

## Closing comments
Enterprise Browser's `DefaultMetaTags` is a very flexible way to "patch" an existing Web application without touching its source code.

As a closing comment here's a sample configuration that zoom the current web page pressing a button:

```
<MetaTag VALUE="KeyCapture~KeyValue:0x79;Dispatch:False;KeyEvent:url('JavaScript:eval('zoom.page=\'1.3\';');')"/>
```

_If you're interested in Enterprise Browser, you can visit us at [EMEA AppForum in London, Oct. 12th-14th](http://bit.ly/EMEAAppForum2015)._
