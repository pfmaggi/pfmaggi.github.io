---
title: Why I like to talk about open source projects
date: 2013-12-28
author: Pietro F. Maggi
summary: About the beauty of open source and the superiority of source code vs (bad) documentation.
tags:
    - "Open Source"
    - "Rhodes"
    - "RhoMobile"
---


Since I joined the RhoMobile group of Motorola Solutions as EIA pre-sale Technical Architect, I started to get disparate question regarding features of the Suite.

Something on the line:

-  How is possible to launch an external application from a Rhodes one? *I tried different APIs and nothing seems to work...*
-  Looking on the support matrix of the APIs for different OSes, it seems that *X* is supported on platform *Y* but *I'm not able to make it working...*

Usually I try to figure out the answer for these question by myself, it's always useful getting a better understanding of the tool I usually talk about (and it's probably the part of my job that I like the most: finding solutions to problems).

Let's see why this is much easier having to understand how an Open Source project works.

First Question: How is possible to launch an external application from a Rhodes one? I tried different APIs and nothing works...
In this case I start my inspection from the docs pages, some usefully tips from the v2.2 API:

Run an application.

System.run_app(appname, params)
appname	The name of the application on the device to run, such as com.android.music.
params	String. List of parameters for the application; nil if no parameters.

Windows Mobile, Windows CE: appname is path in registry:

```bash
<vendor> <app_name>/<app_name>.exe
```

This is usually recorded in the Key:

```bash
HKLM\Security\AppInstall\[App Name]
```

like:

```bash
HKLM\Security\AppInstall\rhomobile store
```

So, to launch this application from your RhoMobile app, you can use the following code:

```ruby
System.run_app('rhomobile store/store.exe', 'someparams')
```

OK, the syntax is not exactly what I was expecting... this may be the cause of the issue.
Documentation of the same API in v4.0 is not so helpful, no explanation of the differences between OSes and what the parameters are...
Let's see what we've on github rhomobile account.

In the Kitchen Sink demo, there's some code linked to the System API, but no sample executing the run\_app method... but the others methods seems to use the same, unfamiliar, syntax to reference a Windows Mobile app like: `rhomobile simple\_app/simple\_app.exe`

Let's see what we've in  [Rhodes source code for Windows Mobile](https://github.com/rhomobile/rhodes/blob/master/platform/wm/rhodes/rho/rubyext/SystemImpl.cpp) and the implementation seems to indicates that these are the right parameters.


So, it took us a bit more than necessary and the documentation is a bit missing on this topic, however, having the source code available gave us the necessary insight to reach our goal.
