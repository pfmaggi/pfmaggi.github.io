---
title: Android Screen Capture for dual screen devices
summary: From problem to solution and to lazy automation, how to easily capture screenshots on a dual screen device.
date: 2021-07-04
tags: [ Android, Tips&Tricks ]
draft: false
author: Pietro F. Maggi
toc: false
showAuthor: false
meta: true
math: false
hideDate: false
hideReadTime: false
---

## What's the problem?

What is keeping me busy lately are dual screen and foldable devices.  
I do some work around [Jetpack WindowManager][0] and I talk to developers on how to support this new form factors.

Testing new code, I may encounter some "strange behavior" and capture a screenshot is usually a good way to convey my point to someone else.

If you use a tool to capture a screenshot on a dual screen device, you will soon discover that (probably) it assumes that there's only a screen. These tools capture the screenshot of the default screen.
On a dual screen device, there's a 50% chance that they will capture the image of the wrong screen...

As an example, for a device like the Samsung Z Fold 2, the default/primary screen is the beautiful foldable screen inside the device. If the device is closed and you are using the external display, your tool will probably capture the image of the internal screen:  
**You will get a black image**.

## Look for a solution

There's nothing wrong in Android and its low level tools, they perfectly know that there's more than one screen. You can ask it yourself to the surface flinger. On a device like the Samsung Z Fold 2 you will get two distinct displays:

{{< highlight shell "linenos=table" >}}
$ adb shell dumpsys SurfaceFlinger --display-id
Display 19261213734341250 (HWC display 3): port=130 pnpId=QCM displayName=""
Display 19261213734341249 (HWC display 0): port=129 pnpId=QCM displayName=""
{{< / highlight >}}

What about `screencap`? Can it be used to capture both screens?

{{< highlight shell "linenos=table" >}}
$ adb shell screencap -h
usage: screencap [-hp] [-d display-id] [FILENAME]
   -h: this message
   -p: save the file as a png.
   -d: specify the physical display ID to capture (default: 19261213734341249)
       see "dumpsys SurfaceFlinger --display-id" for valid display IDs.
If FILENAME ends with .png it will be saved as a png.
If FILENAME is not given, the results will be printed to stdout.
{{< / highlight >}}

It looks like that the `-d` parameter is what we need. We can then run:

{{< highlight shell "linenos=table" >}}
adb exec-out screencap -p -d 19261213734341250> screen_19261213734341250.png
adb exec-out screencap -p -d 19261213734341249> screen_19261213734341249.png
{{< / highlight >}}

To capture an image from each screen.

## Automate

So far, so good. My problem is that lately I'm finding myself capturing a lot of screenshot, so I finally decided to automate this process modifying a script by my friend [Daniele](https://twitter.com/danybony_).

Here is the end result:

{{< highlight shell "linenos=table" >}}
#!/bin/bash

DEVICES=`adb devices | grep -v devices | grep device | cut -f 1`
for device in $DEVICES; do
    DISPLAYS=`adb -s $device shell dumpsys SurfaceFlinger --display-id | cut -d" " -f 2`
    for display in $DISPLAYS; do
        echo "Capturing from $display on $device"
        output="screen_$(echo $device)_$(echo $display)_$(date +%Y%m%d_%H%M%S).png"
        adb -s $device exec-out screencap -p -d $display > $output
    done
done
{{< / highlight >}}

This script capture a screenshot for the displays of all the devices connected to your computer.

I've this script in my personal `~/bin` folder set as executable and I'm running it when needed. I though to write this short notes in case you are also doing some experiments with dual screen devices.

As usual... **it works on my machine™**

[0]: https://developer.android.com/jetpack/androidx/releases/window
