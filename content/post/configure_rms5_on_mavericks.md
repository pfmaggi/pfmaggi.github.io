---
title: Configuring RhoMobile Suite v5.0 on a clean OS X Mavericks machine
summary: Obsolete - Step by step guide on how to install RhoMobile Studio v5.0 on a clean OS X Mavericks machine
date: 2014-08-11T09:57:10+02:00
author: Pietro F. Maggi
keywords: [OS X, RhoMobile]
tags: [OS X, RhoMobile]
topics: [RhoMobile]
draft: false
type: post
---

# **2019 Update**

**The information in this post are obsolete. Please refer to the official RhoMobile documentation for best practices and setup guide.**

# RhoMobile Suite v5.0
The new main release of Rhodes, RhoElements and RhoConnect had just been released a couple of weeks ago [during OSCON](http://newsroom.motorolasolutions.com/Feature/See-RhoMobile-in-Action-at-OSCON-2014-4a61.aspx). It's now time to install it and start building some mobile apps!

In this post I'd like to help you installing RMSv5.0 on a new OS X Mavericks machine and install everything that is needed to build iOS and Android applications, the only pre-requisite is to have a valid OS X Mavericks license installed on a Mac or inside a virtual machine... on a Mac :-).

These are the steps we will follow:

* [Update OS X to the latest version](#OSX_Update)
* [Install XCode](#XCode)
* [Familiarize with the Terminal](#Terminal)
* [Install Java JDK 7](#JDK7)
* [Install HomeBrew](#HomeBrew)
* [Install RVM and Ruby 1.9.3](#RVM)
* [Install git, node and other little utilities](#utilities)
* [Install Android SDK and NDK](#Android)
* [Install RMS v5.0](#RMS5)
* [Create a RhoMobile.com account](#Account)
* [Launching RhoStudio](#Launching)
* [Configure RhoStudio](#Setup)
* [Build a sample app](#Build)

### <a name="OSX_Update"></a>Update OS X to the latest version
The first step is to check that the version of OS X on your machine is up-to-date, a quick stop in the Mac AppStore *Updates* tab can give you an idea of what is available.

![One Update waiting to be installed](/images/OSX_RMS5/updates.png "One Update Waiting to be installed")

### <a name="XCode"></a>Install XCode
The next step is to install the latest XCode on you OS. In this guide I'm using XCode 5 as version 6 is still in beta, and we like stable releases!

![XCode 5 available in the Mac AppStore](/images/OSX_RMS5/XCode5.png "XCode 5 available in the Mac AppStore")

Once you've installed XCode, you need to launch it to accept Apple EULA.

![XCode 5 EULA](/images/OSX_RMS5/XCode5_EULA.png "XCode 5 EULA")

### <a name="Terminal"></a>Familiarize with the terminal
During the initial RMS setup some of the steps require that you use the Terminal, also known as [command prompt or console](http://en.wikipedia.org/wiki/Command-line_interface).
The Terminal is already available on OS X, and you can find it inside the Utility folder in your Applications.

![Launching Terminal](/images/OSX_RMS5/LaunchTerminal.png "Launching Terminal")

Once you've launched the Terminal could be **very** useful to keep it into the dock, as we will need it for some of the steps to complete RMS setup.

![Keep Terminal in the dock](/images/OSX_RMS5/KeepInDock.png "Keep Terminal in the dock")

### <a name="JDK7"></a>Install Java JDK7
OS X Mavericks comes without any Java environment installed, "the easiest way" to install it, is to open the terminal and type:

```sh
java -version
```

This will pop up a windows asking if you want to install Java, the answer is a resounding YES!

![No Java environment](/images/OSX_RMS5/NoJava.png "No Java Environment")

Selecting **More Info...** will open the Safari browser on the Oracle website, you can select the most up-to-date JDK v7 for OS X to download

![Download Java](/images/OSX_RMS5/JavaDownload.png "Download Java")

and Install

![Install Java](/images/OSX_RMS5/JavaInstall.png "Install Java")

When the install is complete you can test that the java environment is now available:

![Java is installed](/images/OSX_RMS5/JavaIsInstalled.png "Java is installed")

### <a name="HomeBrew"></a>Install HomeBrew
[HomeBrew](http://brew.sh/) is an open source package manager, working from the command line, that help setting up open source application on OS X.


```sh
ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"
```

Paste that at the terminal prompt, execute it and follow the on-screen instruction accepting all the defaults.
Next step is to check that HomeBrew is setup correctly:

```sh
brew doctor
```

If everything is setup correctly you get the message: _Your system is ready to brew_:

![Your System is ready to Brew](/images/OSX_RMS5/ReadyToBrew.png "Your System is ready to Brew")

If you get a different message with some missing or misconfigured software you can follow the on-screen-instruction or you can find further information on the [HomeBrew Wiki](https://github.com/Homebrew/homebrew/wiki).

### <a name="RVM"></a>Install RVM and Ruby 1.9.3
OS X Mavericks comes with Ruby v2.0 installed by default, however RMSv5 requires Ruby v1.9.3. Overall working with Ruby can require to switch ruby environment often and to release this pain [some utilities has been created]({{ relref . "post/rvm.md" }}).

In this case we're going to install RVM and use it to install Ruby v1.9.3.
First we need to install some prerequisite using HomeBrew:

```sh
brew tap homebrew/versions
brew install gcc46
brew cleanup
brew doctor
```

then install RVM itself together with ruby 1.9.3:

```sh
\curl -sSL https://get.rvm.io | bash -s stable --ruby=1.9.3
```

Then you need to close the terminal and relaunch it to ave the **RVM** environment available in the command line.

To check that everything is setup correctly you can do:

```sh
rvm list
```

and you should get the reply:

```sh
rhomobile$ rvm list

rvm rubies

=* ruby-1.9.3-p547 [ x86_64 ]

# => - current
# =* - current && default
#  * - default

```

In this case Ruby v1.9.3 is the current and default ruby environment. If this is not the case, you can set it up as the default ruby using the command:

```sh
rvm alias create default 1.9.3
```

To get more information on RVM you can check the documentation available on [its website](https://rvm.io/).

### <a name="utilities"></a>Install git, node and other little utilities
RMS uses git _a lot_, to sync projects with RhoHub, and is overall a nice tool. It's already installed on Mavericks, but is version 1.8.5.2, better to keep an up-to-date version using HomeBrew:

```sh
brew install git
git --version
```

This will install git v2.0.4 (at the moment of writing this).

If asking to the system for the installed git version still report 1.8.5.2:

```sh
git --version
git version 1.8.5.2 (Apple Git-48)
```

Then you need to modify the PATH environment as suggested using the command:

```sh
rhomobile$ brew doctor
Please note that these warnings are just used to help the Homebrew maintainers
with debugging if you file an issue. If everything you use Homebrew for is
working fine: please don't worry and just ignore them. Thanks!

Warning: /usr/bin occurs before /usr/local/bin
This means that system-provided programs will be used instead of those
provided by Homebrew. The following tools exist at both paths:

    git
    git-cvsserver
    git-receive-pack
    git-shell
    git-upload-archive
    git-upload-pack

Consider setting your PATH so that /usr/local/bin
occurs before /usr/bin. Here is a one-liner:
    echo export PATH='/usr/local/bin:$PATH' >> ~/.bash_profile
```

So, using the command:

```sh
echo export PATH='/usr/local/bin:$PATH' >> ~/.bash_profile
```

Solve the issue (after closing and restarting the **Terminal**).


The second application that we need to install is NodeJS, this is needed by RhoConnect Push and to support RhoConnect Source Adapters in JavaScript. Again, we use HomeBrew for this:

```sh
brew install node
```

The Latest application we need to support RhoConnect is [Redis](http://redis.io), again we can use HomeBrew to install it:

```sh
brew install redis
```

Then we can install at least one command line utility that can be very handy looking for "stuffs" inside source codes: _The Silver Searcher_:

```sh
brew install the_silver_searcher
man ag
```

As always, using **man** in the command line will open the documentation for the command, in the case of the silver searcher, the command is **ag** (like the Silver symbol).

### <a name="Android"></a>Install Android SDK and NDK
Next step is to install what is needed to build RhoMobile Android Applications. You need to remember that to compile RhoMobile native applications you need to have the target OS tool chain. For Android this means the SDK and the NDK.

Navigate to the [Android developer tools page](http://developer.android.com/sdk/index.html) and download the stand-alone Android SDK tools for Mac:

![Android SDK Tools for Mac](/images/OSX_RMS5/AndroidSDK.png "Android SDK Tools for Mac")

When the download is complete you can install the SDK in the **Development** folder, again from the command line:

```sh
cd ~/Downloads/
mkdir ~/Development
unzip android-sdk_r23.0.2-macosx.zip -d ~/Development/
```

This will create **android-sdk-macosx** under your home folder. To add the sdk to your PATH, use the command:

```sh
echo export PATH='$PATH:~/Development/android-sdk-macosx/tools' >> ~/.bash_profile
```

closing and relaunching the Terminal will make this new PATH available. You can now launch the Android SDK manager using the command:

```sh
android
```

And install the latest platform tools and the Platform SDK (not shown in the image is the Android Support Library that is always nice to have installed):

![Update Android SDK](/images/OSX_RMS5/UpdateAndroidSDK.png "Update Android SDK")


Once you've installed these packages, you can close the Android SDK Manager and add the now available platform tools folder to your PATH:

```sh
echo export PATH='$PATH:~/Development/android-sdk-macosx/platform-tools' >> ~/.bash_profile
```

closing and relaunching the Terminal will make this new PATH available. You can now launch the Android Debug Bridge using the command:

```sh
adb devices
```

If you plan to develop using Motorola Solutions (or Symbol) Android devices, you need to modify an Android configuration file:

```sh
rhomobile$ android update adb
adb has been updated. You must restart adb with the following commands
	adb kill-server
	adb start-server
echo -e "0x05e0\n0x0414" >> ~/.android/adb_usb.ini
```

Now attaching your device you can see it with:

```sh
adb kill-server
adb devices
```


Next step is to install the Android NDK, containing the C/C++ compiler for Android, [download and install the Mac OS 64-bit release](http://developer.android.com/tools/sdk/ndk/index.html), following Google [instructions](http://developer.android.com/tools/sdk/ndk/index.html#Installing). Again, from the command line:

```sh
cd ~/Development/
tar xzvf ~/Downloads/android-ndk32-r10-darwin-x86_64.tar.bz2
```

### <a name="RMS5"></a>Install RMS v5.0
For this step we can take the info from the [RhoMobile official documentation](http://docs.rhomobile.com/en/5.0.0/guide/rhomobile-install#mac-os).

first of all we need to download the RMS5 image file from [RhoMobile website](). Opening the image will show it's content, first step is to copy the **Motorola RhoStudio** in the application folder:

![RMS5 image file content](/images/OSX_RMS5/RMS5_Package.png "RMS5 Image file content")

Then we've some additional steps to do from the Terminal... yes, again from the command line :-)

**NOTE** I previously used **sudo** at this step and this produce a series of side effects. Just be sure to run the "Install gems" script with your user!

```sh
cd /Volumes/Motorola\ RhoMobile\ Suite\ Installer/
./Install\ gems 

Select Ruby version to install gems:
1) ruby-1.9.3-p547
2) ruby-1.9.3-p547@global
#? 2

Using /Users/rhomobile/.rvm/gems/ruby-1.9.3-p547 with gemset global
ruby 1.9.3p547 (2014-05-14 revision 45962) [x86_64-darwin13.3.0]

Do you want to install gems with 'sudo' command prefix? [yN]  N

```

When the gems setup is complete, you can install the support for RhoConnect Push server:

```sh
cd /Volumes/Motorola\ RhoMobile\ Suite\ Installer/
sudo ./Install\ rhoconnect-push
Password:

Do you want to install rhoconnect-push with 'sudo' command prefix? [Yn]  Y
```

### <a name="Account"></a>Create a RhoMobile.com account
Version 5 of the RhoMobile Suite has a [new licensing](http://rhomobile.com/rhopricing.html), that is a yearly subscription available in different levels, starting from a free one, up to an Enterprise grade.

Whatever the level you choose, you need to create an account on the RhoMobile.com website to use the Suite, both from RhoStudio and from the command line tools.

### <a name="Launching"></a>Launching RhoStudio
RhoStudio and the whole RhoMobile Suite is now installed on your Mac, but you still need some steps to finish the configuration and start doing some coding. First of all launch RhoStudio from the Application folder (launch the 64-bit version) and select the second ruby environment for it.

![Launch RhoStudio](/images/OSX_RMS5/LaunchRhoStudio.png "Launch RhoStudio")

![Select Ruby Environment](/images/OSX_RMS5/SelectRuby.png "Select Ruby Environment")

At this moment you will get a request to install a J6SE environment, go ahead and install it (the JDK we installed is for the Android SDK, this is used to run Eclipse)

![Java 6 Runtime](/images/OSX_RMS5/Java6SE.png "Java 6 Runtime")

Finally we've RhoStudio running, we need to configure it!

BTW: if the first launch ends with and error in RhoStudio console, simply close it and relaunch RhoStudio again, you end up with a login request, simply use the RhoMobile account you created at the previous step.

![RhoStudio first launch](/images/OSX_RMS5/FirstRhoStudio.png "First RhoStudio Launch")

### <a name="Setup"></a>RhoStudio Setup
There are some parameters to setup in RhoStudio before being able to build any project. Opening the settings windows using **Command-,** you can setup:

* JDK Path = /Library/Java/JavaVirtualMachines/jdk1.7.0_67.jdk
* Android SDK Path = <your user folder>/Development/android-sdk-macosx
* Android NDK Path = <your user folder>/Development/android-ndk-r10

as shown in these images:

![JDK Path](/images/OSX_RMS5/RhoStudioParam_1.png "JDK Path")

![Android SDK and NDK path](/images/OSX_RMS5/RhoStudioParam_2.png "Android SDK and NDK path")

Another (easier?) way to configure Rhodes is doing it from the command line:

```sh
rhodes-setup
We will ask you a few questions below about your dev environment.

JDK path (required) (/Library/Java/Home):
Android SDK path (blank to skip) (): /Users/rhomobile/Development/android-sdk-macosx/
Android NDK path (blank to skip) (/Users/rhomobile/Development/android-ndk-r10):
Windows Mobile 6 SDK CabWiz (blank to skip) ():
BlackBerry JDE 4.6 (blank to skip) ():
BlackBerry JDE 4.6 MDS (blank to skip) ():
BlackBerry JDE 4.2 (blank to skip) ():
BlackBerry JDE 4.2 MDS (blank to skip) ():

If you want to build with other BlackBerry SDK versions edit: /Users/rhomobile/.rvm/gems/ruby-1.9.3-p547@global/gems/rhodes-5.0.2/rhobuild.yml
```

### <a name="Build"></a>Build your first RhoMobile application
I'll cut this short at this moment, you should have everything setup at this moment and you should be able to follow the instructions on [RhoMobile website](http://docs.rhomobile.com/en/5.0.0/guide/creating_a_project). You can reach out to me if you've any question and I plan to add another post with some "post-setup" optimization in the near future.


