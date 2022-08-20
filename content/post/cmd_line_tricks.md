---
title: Little command line tricks
date: 2014-01-10T19:37:45+01:00
author: Pietro F. Maggi
summary: More than a blog this is a note to self. Don't forget this little tricks!
keywords: [OS X, Command Line, Tricks]
tags: [OS X, Command Line, Tricks]
topics: [Tricks]
draft: false
type: post
---

This is a different blog, more a remainder for me than anything else. Hope that someone else can find these useful... they're for me!

### Recover a failed download with curl:

```sh
curl -C - -o partially_downloaded_file 'www.example.com/path/to/the/file'
```

### [Burn a playable DVD from a VIDEO_TS folder](http://hints.macworld.com/article.php?story=20070612161317338)

After searching the forums and trying various things, I still couldn't find a quick, reliable, free method of burning a VIDEO_TS folder to a pure UDF DVD,
so that it would play in regular DVD players, as well trigger DVD Player to start up automatically. Anyway, as often is the case, Terminal had the answers.
Just type in this command and change the paths to suit:

```sh
hdiutil makehybrid -udf -udf-volume-name DVD_NAME -o MY_DVD.iso /path/to/VIDEO_TS/parent/folder
```

Make sure that /path/to/VIDEO\_TS/parent/folder is the path to the folder containing the VIDEO\_TS folder, not the VIDEO\_TS folder itself.
Once the .iso file has been created, drag this to Disk Utility and hit the Burn button.

### Generate a pdf from a markdown file
```sh
markdown <File.md> | htmldoc --cont --headfootsize 8.0 --linkcolor blue --linkstyle plain --format pdf14 - > FileFormat.pdf
```

# Android related Commands

### Taking screenshot of an Android device
```
adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > screen.png
```

### Launch browser and navigate to URL on android devices
```sh
adb shell am start -a android.intent.action.VIEW -d URL
```
