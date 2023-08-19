---
title: April 2021 Programming notes
summary: Summary of interesting topics and articles explored in April 2021.
date: 2021-05-02T17:00:00+01:00
tags: [Notes, Programming]
draft: false
author: Pietro F. Maggi
---

## Design By Contract

I still remember the first time I read about this technic. I felt into a rabbit hole to learn about with Eiffel and object oriented programming. We're easily talking about 20+ years ago.

What I didn't know is that [Design by Contract is also available in Ada2012][1].

I should not be surprised by this! [Wikipedia][2] reports a long list of languages that now support this technique natively.

During the years I've used DbC in some embedded projects in C.
This is a nice technique but, for what I was using it for there are better tools. My main use case was  to enforce not-null or in-range parameters. Newer languages eliminate or reduce these issues altogether.

[1]: https://learn.adacore.com/courses/intro-to-ada/chapters/contracts.html#design-by-contracts
[2]: https://en.wikipedia.org/wiki/Design_by_contract

## A comprehensive list of various text to diagram tools

Sometimes I need to add a diagram to a document or to a presentation. I'm not a fan of drawing these manually. It would be much easier to have something similar to Markdown but to generate these diagrams!

Obviously there is already such a tool! The reality is that there are many of such tools, starting from [Ditaa][3].

My usual problem is that, when I need such a tool, I've to install on the machine I'm working on (and that change too much frequently).

Lately I've found [this list of online tools][4] that generate diagrams from a text representation. If you can use an online tool for this job, this is a great resource.

[Kroki][5] is my new favorite tool for this task as it supports a lot of formats.

An offline tool that gives a bit more control is [asciitosvg][6], exactly as the name says.

[3]: http://ditaa.sourceforge.net/
[4]: https://xosh.org/text-to-diagram/
[5]: https://kroki.io/
[6]: https://github.com/asciitosvg/asciitosvg

## The Problem with Gradle

I found a lot to agree with Bruce Eckel in this article on his blog: [The Problem with Gradle](https://www.bruceeckel.com/2021/01/02/the-problem-with-gradle/).

I spend most of my time writing Android code in a sort of truce with [`gradle`][7]. But I also remember how it was before having gradle available when the "build tool" was [`ant`][8].

Personal opinion, `gradle` is not bad, but there's too much magic involved.

[7]: https://gradle.org/
[8]: https://ant.apache.org/

## The Modern Java Platform - 2021 Edition

James Ward is the co-host, with Bruce Eckel, of the [Happy Path Programming][9] podcast. The previous topic about Gradle is also covered there.

James' has a very good article (also covered in the podcast) on [The Modern Java Platform][10], updated to the year 2021.

No big surprise there, but it is nice to see everything in a single article:

* JVM Languages: Scala, Kotlin and obviously Java 11-16
* Tooling
* Frameworks
* Containers
* Serverless and how to avoid the JVM overhead

It is a great investment of time to get up to speed in the Java Platform if you don't use it frequently.

[9]: https://anchor.fm/happypathprogramming
[10]: https://jamesward.com/2021/03/16/the-modern-java-platform-2021-edition/

## 3D Printing for Builders

I periodically visit Paul Lutus website to see what he's up to. He's one of my youth heroes, from the Apple II days when he created the Apple Writer and other amazing programs, including a couple of Forth for that machine while living in a [cottage][11].

He's now an interesting article that goes in great details on [how to use a 3D printer][12] and get the best possible result from an enthusiast level machine.

[11]: https://www.atariarchives.org/deli/cottage_computer_programming.php
[12]: https://www.arachnoid.com/3DPrinting/index.html

## More Blockchain / Crypto non-sense

The [Chia cryptocurrency][13] will start to trade on May 3rd, and [hard drives and SSD prices are already skyrocketing][14]. Instead of Proof of Work we have Proof of Space. The idea is that this should not involve custom hardware and gigantic energy consumptions. The reality is that the initial creation of the data to fill the hard drive is still CPU/GPU intensive, and best adapted to SSD, while the long term farming doesn't require much energy and hard drives are cost effective. So the two process needs different hardware [to plot][15] and [to farm][16].

I've no sympathy for people burning Earth's limited resources to participate in a get-rich-fast scheme.

[13]: https://www.chia.net/
[14]: https://www.tomshardware.com/news/hard-drive-prices-skyrocket-asia-scalpers-making-bank
[15]: https://github.com/Chia-Network/chia-blockchain/wiki/Reference-Plotting-Hardware
[16]: https://github.com/Chia-Network/chia-blockchain/wiki/Reference-Farming-Hardware

## Interesting applications

### xbar

[xbar][20] is [bitbar][21] evolution rewritten in Go. A macOS application that allows to run script and put information in macOS menubar.

I've used bitbar for a long time. However I'm not interested investing time in macOS anymore and I don't plan to spend time migrating my bitbar setup.

[20]: https://xbarapp.com/
[21]: https://getbitbar.com/

### Kakoune editor

[Kakoune][22] is a modern modal editor, similar to Vim that is designed around the concepts of **verbs**: d for delete and **object**: w for word. It looks very interesting, but also it looks like another rabbit hole. I'm finding myself moving frequently from machine to machine and using a tool like Kakoune will require to install and configure it on each new machine.

* [The first two hours of Kakoune in two minutes][23]

I file this in the *interesting: come back again*, category... *also known as Yak Shaving category*.

[22]: https://kakoune.org/
[23]: https://kakoune-editor.github.io/community-articles/2021/01/01/first_two_hours_in_two_minutes.html

### Hammerspoon

[Hammerspoon][24] is a desktop automation tool for OS X bridging various system level APIs into a Lua scripting engine. It's not new, I've never used it, and looks really interesting.

Similarly to xbar, I'm winding down my interest in macOS and I'm not planning to spend time with Hammerspooon anytime soon.

[24]: https://www.hammerspoon.org/go/

## Interesting Reads

### Reverse Engineering the source code of the BioNTech/Pfizer SARS-CoV-2 Vaccine

With all the pain and suffering that Covid-19 inflicted (and still is) to the world, the introduction on the market of mRNA based vaccine is a great silver lighting.

[This article][20] takes a character-by-character look at the source code of the BioNTech/Pfizer SARS-CoV-2 mRNA vaccine. Not exactly my cup of tea, but nevertheless facinating and a great read.

[20]: https://berthub.eu/articles/posts/reverse-engineering-source-code-of-the-biontech-pfizer-vaccine/

### Real-time tracking of serotonin, dopamine opens new window to the brain

[This article][21] covers how Deep Brain Stimulation (DBS) can boost dopamine production in Parkinson's patients.

[21]: https://newatlas.com/medical/serotonin-dopamine-real-time-tracking-brain/
