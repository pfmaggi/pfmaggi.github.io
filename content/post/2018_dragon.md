---
title: "Here be Dragons - The Enterprise Mobile Security Journey"
summary: "This blog started out as a series of notes for a talk I've presented in April 2018 at a couple of events (Droidcon Dubai and Droidcon Turin) with my Zebra Technologies hat. Feel free to provide comments and feedback."
date: 2018-04-14T21:30:48+01:00
draft: false
---

**Note:**

    This blog started out as a series of notes for a talk I've presented in April 2018 at a couple of events (Droidcon Dubai and Droidcon Turin) with my Zebra Technologies hat. Feel free to provide comments and feedback.

Looking at what is trendy at technology conferences, you can find a lot of talks about JavaScript frameworks or [functional|concatenative|multi-paradigm] programming languages, so when I started to work on my new talk I felt that I had to explain the title a little bit.

### 1. This talk is not about a programming language

Someone may be disappointed! this looks like a great topic for a talk, but no. This talk is not about the [`Drakon` algorithmic visual programming language that was developed within the Buran space project](https://en.wikipedia.org/wiki/DRAKON).

### 2. Dragon is not about a JavaScript library

Strange to believe, this talk is not about [`dragon.js`, a backbone inspired ES6/ES7 JavaScript framework](https://github.com/chrisabrams/dragon)... or one of the many other JavaScript library named dragon.

## This is a talk about dangerous places 

"Here be dragons" means dangerous or unexplored territories, in imitation of a medieval practice of putting illustrations of dragons, sea monsters and other mythological creatures on uncharted areas of maps.

<span style="display:block;text-align:center">
[![Dragons](/images/2018_here_be_dragons/Dragons.png "Dragons")](https://www.livescience.com/39465-sea-monsters-on-medieval-maps.html)
</span>

The dangerous and unexplored territories that I'm going to cover in this talk are the new scenarios that are appearing in the enterprise mobility space, for dedicated devices.  
Devices that are rolled out in the field to run Line of Business application.

I call this:
# The Enterprise Mobile Security Journey
### *Enough talking, let's dig into the topic starting from the beginning!*

## Who is Zebra Technologies
Zebra makes businesses as smart and connected as the world we live in.

<span style="display:block;text-align:center">
![Zebra Technologies](/images/2018_here_be_dragons/zebra_technologies.png "Zebra Technologies")
</span>

To learn more about Zebra Technologies, you can take a look at the [company website](https://www.zebra.com).

## A year in review
Since 2016 I've been involved in multiple projects explaining to enterprises in EMEA how the new mobile security scenario requires new rules of engagement.  
To explain what I mean, let's revisit how mobile devices have been deployed in the last 15-20 years.

### A brief history of mobile devices deployment
15 years ago, I was implementing a mobile solution on Windows Mobile 2003 for a customer where I built an application that was working in kiosk mode. Part of the implemented solution was the Flash image of the device configuration, that we needed to write on every single device before delivering it to the customer. This is what we now call a "Custom ROM".  
After 8 years the customer was still using my application with the exact same configuration.  
This was not uncommon; this was the norm... The standard way to implement an enterprise mobility project. We called these **GOLDEN IMAGES** and they've been part of these world since the beginning of time (January 1st, 1970).

However, *"We've always done it this way"* is the most dangerous answer in ICT, especially when we look into the security of a solution.

<span style="display:block;text-align:center">
![We've always done it this way](/images/2018_here_be_dragons/We-have-always-done-it-this-way.png "We've always done it this way")
</span>

Let's break down the elements of the solutions we used in the past.

### Custom hardware and enterprise software
Enterprise mobility is not new. At least is not new for the companies that adopted "mobile devices" to improve business processes over the last 40 years.  
We've seen devices evolving during these decades from a custom operative system, based on top of DOS (Digital Research DOS, to be precise) moving over these years to PalmOS, Pocket PC, Windows Mobile and lately Android.

<span style="display:block;text-align:center">
![Symbol's PDT3100](/images/2018_here_be_dragons/pdt3100.png "Symbol's PDT3100")
</span>

The increased size of the mobile market has increased the specialization of these operative systems. Moving away from the concept of a small PC in your pocket (I'm talking about you, PocketPC) to an OS designed for internet-enabled mobile devices. An OS with a new release every year and more than 2 Billion active users in the world.

<span style="display:block;text-align:center">
**THIS CHANGES EVERYTHING!**
</span>

What was once a nice market, with only a few players, is experiencing in the last few years an acceleration that needs to be addressed by the IT organization inside the companies that are deploying enterprise mobility solutions for Line of Business (LoB) application.  
What is a LoB application? It's a critical application for the business of an enterprise, like an inventory or a checkout system for a retailer or the proof of delivery application for a postal company.

But here's there's an impedance mismatch.  
We (WE) need to act as the matching network for two very different worlds:

  1. On one side, an enterprise company, that from the inception of a mobility project to the deployment of the first device can take, easily, a couple of years
  2. On the other side, a consumer ecosystem, with a new OS release every year, billions of users and the full attention of subjects ready to exploit every minimal vulnerability at their own advantage

How can we combine these two worlds?

<span style="display:block;text-align:center">
![Impedance Matching](/images/2018_here_be_dragons/impedence_matching.png "Impedance Matching")
</span>


## Ok, this is really about security!
Hey Pietro, I was thinking that this was a talk on security, not on history!

[Let's take a look at what Wikipedia says about security](https://en.wikipedia.org/wiki/Computer_security):

> ### Cybersecurity, computer security or IT security
> is the protection of computer systems from the theft and damage to their hardware, software or information, as well as from disruption or misdirection of the services they provide.

The most important topic here is the prevention of theft of data and preventing services disruptions.

Talking about Line Of Business application, this means that we need to guarantee the security of a solution during all of its lifecycles.
### So... This is all about security!
How can we guarantee the lifecycle that an enterprise company requires (working with the same device for 5+ years) keeping the device secure?

It's simple, providing security updates for at least five years...

### LifeGuard for Android
In 2017 Zebra Technologies introduced [LifeGuard for Android](https://www.zebra.com/lifeguard).

<span style="display:block;text-align:center">
![LifeGuard for Android](/images/2018_here_be_dragons/lifeguard_0.png "LifeGuard for Android")
</span>

LifeGuard™ for Android™ is Zebra’s software security solution that extends the lifecycle of Zebra Android enterprise mobile computers.  
LifeGuard adds years of OS security support after consumer support stops to match the enterprise hardware lifecycle, helping your business significantly lower total cost of ownership.

With LifeGuard, you will receive extended security support, predictable periodic security updates and legacy OS security support when transitioning to a newer OS. Frequent updates will enhance your security and LifeGuard makes them easy to install at your discretion, either locally, or remotely via Enterprise Mobility Management (EMM).

<span style="display:block;text-align:center">
![LifeGuard for Android](/images/2018_here_be_dragons/lifeguard_1.png "LifeGuard for Android")
</span>


### Problem solved?
> *"In theory, there is no difference between theory and practice. But, in practice, there is."*
> - Jan L.A. van de Snepscheut 

Not really, here is where the story became a bit messy, where Theory and Reality start to diverge.

Here's is where the impedance mismatch reveals its effect.

Zebra Technologies has released more than 250 security patches for its devices in less than one year, but some customers are still using the same Operative System that they deployed initially on their devices more than two years ago...

<span style="display:block;text-align:center">
![WAT](/images/2018_here_be_dragons/wat.png "WAT")
</span>

Making available the security updates doesn't mean that these customers are going to install them on the devices!

## Why is this happening?
Pushing updates to enterprise devices is not an option:
  
  - Updates need to be controlled by the IT organization to happens only in particular time windows
  - Updates need to be validated before being installed on devices to guarantee that no service is going to be disrupted

And there are then some constraints on the connectivity:

  - A lot of these devices works in a closed network, without internet access
  - A lot of these devices are connected to networks without the required bandwidth to transport the amount of data required for the updates

And to close the circle:

  - A lot of the organization involved are not equipped to test new OS version/new security updated timely

We (WE) need to find and promote a solution.

## Solution? A work in progress

### 1. Measuring what is installed on the devices
First, we need to know what is installed on the device, having a clear picture is key in this cases:

  - Which OS version is installed (the build number)
  - Which security patch is installed
  - other relevant SW releases that we need to collect
  - Any HW information that may be relevant

<span style="display:block;text-align:center">
![Measure twice, cut once](/images/2018_here_be_dragons/measure2cut1.png "Measure twice, cut once")
</span>

<span style="display:block;text-align:center">
**VISIBILITY IS AN ASSET**
</span>

During the last couple of years, Zebra has introduced on the market a couple of visibility solution following the market demands.

<span style="display:block;text-align:center">
![Visibility that's visionary](/images/2018_here_be_dragons/visibility.jpg "Visibility that's visionary")
</span>

### 2. Simplify deployment
When you have a device and you are in charge to decide if you need an OS update or not. It's easy!  
Stuff starts to be complicated when your Android device is far-far away.

<span style="display:block;text-align:center">
![Far Far Away Android](/images/2018_here_be_dragons/far-far-away_android.png "Far Far Away Android")
</span>

... and you have 1000 devices connected to a closed network, with limited bandwidth.

In this case, the solution is to have an EMM system that supports you in the management of these devices.

Regarding the limited bandwidth... keep in mind the scale of some of these deployments. Once a customer told me that:

> *Pietro, if I’ve to deploy all the security updates your release for one year, I’m going to move more than 14TB over my network*

One solution is to adopt some relay servers for the EMM software you choose to distribute these updates.  
We're investigating the possibility to have the update download on a single device and then having this device to behaves as a server for the other devices in the same location. This will allow reducing the overall bandwidth requirement but creates other concern regarding the stability of the solution.

### 3. Simplify testing
There's a big elephant in the room that we've not yet touched: a lot of enterprise companies are not equipped to automate their tests.

And is not just about installing a new security update with the idea that it doesn't change much.
We have devices with verify boot and these means that a security update needs to be signed for a specific device.
Security updates nowadays are only available for specific OS build. Remember that Zebra released more than 250 updates in just one year targeting only some of the maintenance releases we had on our devices. This number can grow exponentially to an unmanageable value if don't put some limit on which OS release you're going to support!  
These means that sometimes you need to move to a newer OS just to keep receiving security updates.

For some companies, doing a complete test takes weeks. To be fair, it's not just about testing the application on the device. That's the easy part (the most of them have not yet automated). The hard problem is testing the real-world environment, the data connection, the WIFI roaming and a lot of other variables.

This is the most critical challenge that now Zebra Technologies is targeting to allows companies to move from a "Golden Image" mentality to a "Rolling Release".

<span style="display:block;text-align:center">
![Testing as a service](/images/2018_here_be_dragons/testing_services.jpg "Testing as a service")
</span>

## Call to action
These are dangerous AND unexplored territories. 

<span style="display:block;text-align:center">
[![Dragons](/images/2018_here_be_dragons/Sea_Monster_Chet_Van_Duzer.jpg "Dragons")](https://www.livescience.com/39465-sea-monsters-on-medieval-maps.html)
</span>

I know that these are real pain points for our customers.

It's a great opportunity for software developers and entrepreneurs... There're enterprises looking for a solution in this space!

## Media References in the presentation
* [Drakon](https://en.wikipedia.org/wiki/DRAKON)
* [dragonJS](https://github.com/chrisabrams/dragon)
* [Sea Dragon](https://www.livescience.com/39465-sea-monsters-on-medieval-maps.html)
* [Carstenz Pyramid](https://commons.wikimedia.org/wiki/File:Puncakjaya.jpg)
* [Giant Rubber Duck](https://commons.wikimedia.org/wiki/File:Rubber_Duck_in_Nakanoshima,_Osaka_in_201509_003.JPG)
* [Impedence Matching](https://commons.wikimedia.org/wiki/File:LMatchingNetworks.svg)
* [IT Crowd Gif](https://giphy.com/gifs/the-it-crowd-chris-odowd-F7yLXA5fJ5sLC)
* [Far Far Away Android](https://farfarawayalbum.bandcamp.com/album/far-far-away)
* [Testing as a service](https://www.omkarsoft.com/testing-services/)
* [Sea Monster](https://commons.wikimedia.org/wiki/File:Sea_Monster_Chet_Van_Duzer.jpg)
