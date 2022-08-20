---
title: Boox Max 3 as a Raspberry Pi monitor
summary: Boox Max 3 can be used as a HDMI monitor, let's see how to configure RaspberryPiOS to use this eInk panel as a console monitor.
date: 2020-11-08T19:00:00+01:00
tags: [RaspberryPi, Boox, eInk, Linux]
draft: false
author: Pietro F. Maggi
---

## Starting from scratch

I like reading books but it is not always convenient to travel with two or more technical books while commuting to the office or travelling to a remote conference (remember those times).

For this reason I lately bought a [Boox Max 3](https://www.boox.com/max3-3/), a 13.3" eInk reader that is my travel companion between by bedroom and my office. This is the kind of commuting I'm doing in 2020.

Anyway the Boox is an great machine, here is a review by [My Deep Guide](https://twitter.com/mydeepguide) on [YouTube](https://www.youtube.com/watch?v=SEHFkqHtu_g) that covers a lot of the capabilities.

One of the interesting features of this device is that it has a HDMI input port to use it as a monitor. You can take a look at this other [video](https://www.youtube.com/watch?v=Kdn7J_M5B4o), again by My Deep Guide, that covers this feature and the possibility to use the Boox Max 3 with with a USB-C hub (spoiler alert, you cannot charge the Max 3 while using the USB-C port).

## Boox Max 3 as a monitor for the Raspberry Pi

I'm not interested using an eInk as a monitor for a laptop. I had instead the idea to have a distraction-less device to write. Combining a Raspberry Pi, the Boox Max 3 and a mechanical keyboard.  

These seems a fancy setup, but having all the pieces laying around it was an easy test to put in place.

### RaspberryOS lite

My idea is to work from the command line, from the terminal, with an editor like vim or emacs. No mouse involved.
Something similar to what described in [this Reddit post](https://www.reddit.com/r/linuxmasterrace/comments/5porwq/raspberry_pi_zero_distraction_free_word_processor/), but with a much larger screen.

First test was to just put a recent image of the OS on a SDCard and test the result with a Raspberry Pi 2. A quick test just to check that I have to use some custom configuration for the screen setup.

### Boox Max 3 as a monitor

Connecting the Max 3 to a mac, I can use it as a monitor with the default resolution of 2200x1560 without having to do anything. Why the Raspberry can't see it in the same way?

Probably some issue with the frequency (did I say that I couldn't find any spec on the vendor's website?).

Some research on the Web returned a [video on youtube](https://www.youtube.com/watch?v=LKtD3CJE9OU) with some parameters to use in the Raspberry's `config.txt`:

    hdmi_cvt=2104 1560 30 3
    hdmi_group=2
    hdmi_mode=87
    hdmi_drive=2

It is a start!

**Note** I'm not suggesting to try random parameters seen on the web in your devices. This was an hint that I should look in Raspberry's documentation to check what those parameters are and if they make sense!

### config.txt

Raspberry Pi's documentation has a [video section](https://www.raspberrypi.org/documentation/configuration/config-txt/video.md) that is quite comprehensive on the options available in `config.txt` to configure the HDMI output of these boards.

My suggestion is to start asking to the monitor if it can provide some additional information of what is does support.
To do this we can use the `tvservice` command line utility to log the information of the connected monitor to a file:

    $ tvservice -d edid.dat
    Written 256 bytes to edid.dat

We can then convert this file with `edidparser` to generate a readable text file:

    $ edidparser edid.dat > edid.txt

**Note** These utilities are pre-installed in RaspberryOS, including the lite. I was as surprised as you reading about these utilities on [this article](https://opentechguides.com/how-to/article/raspberry-pi/28/raspi-display-setting.html)

The interesting part, at least for me, are:

    Enabling fuzzy format match...
    Parsing edit.dat...
    HDMI:EDID version 1.3, 1 extensions, unknown aspect ratio
    HDMI:EDID features - videodef 0x80 !standby !suspend !active off; colour encoding:RGB444|YCbCr422; sRGB is not default colourspace; preferred format is native; does not support GTF
    HDMI:EDID found monitor name descriptor tag 0xfc
    HDMI:EDID monitor name is Toshiba-UH2D
    HDMI:EDID found monitor range descriptor tag 0xfd
    HDMI:EDID monitor range offsets: V min=0, V max=0, H min=0, H max=0
    HDMI:EDID monitor range: vertical is 20-120 Hz, horizontal is 1-255 kHz, max pixel clock is 290 MHz
    HDMI:EDID monitor range does not support GTF
    HDMI:EDID failed to find a matching detail format for 2200x1650p hfp:140 hs:60 hbp:220 vfp:5 vs:5 vbp:20 pixel clock:120 MHz
    HDMI:EDID calculated refresh rate is 27 Hz


After trying some of the options reported in the `edid.txt` file I decided to try setting up my custom configuration with a custom mode:

    hdmi_group=2
    hdmi_mode=87

As we saw in the previous section we can use the `hdmi_cvt` parameter to configure the screen. In my case I used the monitor resolution and a 25Hz refresh rate... a bit low for a normal monitor, but not if you are driving an eInk.

So the final configuration that I used is:

    # Custom configuration: <width> <height> <framerate> <aspect> <margins> <interlace> <rb>
    # width        width in pixels
    # height       height in pixels
    # framerate    framerate in Hz
    # aspect       aspect ratio 1=4:3, 2=14:9, 3=16:9, 4=5:4, 5=16:10, 6=15:9
    # margins      0=margins disabled, 1=margins enabled
    # interlace    0=progressive, 1=interlaced
    # rb           0=normal, 1=reduced blanking
    hdmi_cvt=2200 1560 25 3
    hdmi_group=2
    hdmi_mode=87
    # Normal HDMI mode (sound will be sent if supported and enabled)
    hdmi_drive=2

I've done some other customization on the font and the shell, but at the end, this is unlikely to be a setup that I want to use long term.

I like the idea of a simple, single task, device to use to write with little or no distraction. But this is not it.
I'll need to try something different. Let me know if you have any suggestion, DM are open on my [twitter account](https://twitter.com/pfmaggi).

### Edit. Last minute addition

I forgot to post images of the result, as I wrote, I was hoping for something better.

<span style="display:block;text-align:center">

![Raspberry Pi booting](/images/20201109_raspberry_eink/boot.png "Raspberry Pi booting")

</span>

<span style="display:block;text-align:center">

![vimtutor - Black on White](/images/20201109_raspberry_eink/vimtutor.png "vimtutor - Black on White")

</span>

<span style="display:block;text-align:center">

![Cow Say!](/images/20201109_raspberry_eink/cow_say.png "Cow Say!")

</span>
