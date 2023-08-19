---
title: Sticking to 1Password 6
description: I've been a long time 1Password user, since version 4, but I never upgraded to AgileBits subscription model and version 6 is the last 1Password version I can use. Sometimes I made mistakes and I've to revert back to this version.
date: 2023-03-25
author: Pietro F. Maggi
draft: false
meta: true
math: false
toc: false
hideDate: false
showAuthor: true
hideReadTime: false
tags:
    - "Software"
    - "Personal"
---

I loved 1Password since the first time I installed it many years ago. I probably got in some OSX Indy developer bundle and I found it liberating.

Over the years, I kept paying for the new version as soon as it was released but around version 7, Agile Bits (the company behind 1Password) decided to transition to a subscription model.

I can understand their decision as I worked as sofware engineer for many years and I can agree that in some scenarios having a 1Password subscription is a no brainer. But not for me so far.

It may happen in the coming years that my family requirements will increase and teaching my children how to corretly handle passwords with a tool like 1Password will be a smart thing to do. But, till that point, I've very limited requirements for 1Password. Requirements that makes very difficult to justify a recurring payment.

For this reason I decided to keep using version 6 with all its limitation but also with the older business model of a perpetual license.

The problem is that sometimes I try to fix something or I'm trying something new and I forget what was the last version of 1Password that I can use. This last time it was to get a Safari extension installed (1Password 6 works for me in Firefox and that is usually more than enough).

Long story short, from Safari, looking for an extension, I installed 1Password 7 from the mac app store...

Once I understood my mistake I removed it and got back to my lovely obsolete version 6... but it was too late. Unlocking it was not working as expected. My password were available in 1Password Mini and through the extension in Firefox, but the main UI was stuck. With no way to use it. Another issues was that, if 1Password Mini was already open, trying to launch 1Password was ending with a dialog showing an error that it was not possible to connect to 1Password Mini.

I searched for a while till finding this [post][1] that points to some folder inside `~/Library/Group Containers/`.

Looking there, I had two different folders:

- `2BUA8C4S2C.com.1password`
- `2BUA8C4S2C.com.agilebits`

Soo, the first thing to try, was to move these two folders somewhere else and relaunch 1Password 6... and IT WORKS.  
(yes the first thing to try was also the last one... sometimes it happens).

Ok, so, I solved my problem in this way.

For the record, before finding [that article][1] I also tried to reinstall 1Password 6 from the installer available on the [Agile Bits website][2] (thanks for keeping legacy version available!). But the only way to solve the issue that I found was to remove those two folders.

[1]: https://1password.community/discussion/120371/1password-failed-to-connect-to-1password-mini
[2]: https://1password.com/downloads/mac/
