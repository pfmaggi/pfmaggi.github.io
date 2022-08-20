---
title: Using Android `sdk.dir` property to locate EMDK's jar Library.
summary: Obsolete - My personal preference is to reference Zebra's EMDK jar library in gradle's dependencies, and this requires to indicate the correct path to the library. So far so good if you're working on a single machine. This short post shows a better way to indicate EMDK library path independently from the machine where you're building your app.
date: 2017-08-08T17:20:00+01:00
tags: [Android, Tips&Tricks, Zebra Technologies]
draft: false
author: Pietro F. Maggi
---

# **2019 Update**

**This post is now obsolete. Please refer to Zebra's EMDK documentation for updated information.**

_Note this post has been published on [Zebra developer portal](https://developer.zebra.com/community/android/android-forums/android-blogs/blog/2017/08/08/using-android-sdkdir-property-to-locate-emdks-jar-library), if you've any comment, probably best to post them over there._

----

Working in Android Studio you can notice that every project contains a `local.properties` file with at least a couple of properties containing the path for Android SDK and Android NDK.

Android Studio populates this information when it creates the project based on the `ANDROID_HOME` environment variable. If this variable is not present in your system Android Studio will find the correct path in other ways, but having `ANDROID_HOME` setup correctly is a really good idea, see the note at the end of this short post.

Given that I usually have in my `build.gradle` file a reference to Zebra's EMDK, look like a good idea to use `sdk.dir` instead than hardcoding the path (like I've done for a too much long time) given that the EMDK resides in Android SDK's add-ons folder.

So, my gradle files now includes:

    Properties properties = new Properties() properties.load(project.rootProject.file('local.properties').newDataInputStream())
    def sdkDir = properties.getProperty('sdk.dir')

so that I can the use the sdkDir variable like:

    provided fileTree(include: ['com.symbol.emdk.jar'], dir: sdkDir+'/add-ons/addon-symbol_emdk-symbol-23/libs/')

Much cleaner and nice :-)

## A note on `ANDROID_HOME`
It's not usually a good idea to include in your revision system the `local.properties` file because this contains information... "local"... to your machine.
Imagine that you're sharing a project with a colleague or with a remote CI build server. In this case everybody will have the Android SDK (and the Zebra's EMDK that resides in the SDK add-ons) in a different folder.

The best option in this case is to avoid to share the `local.properties` file and have the `ANDROID_HOME` environment variable correctly setup so that the build works correctly on every machine.

   * If `local.properties` is not available, Gradle will use `ANDROID_HOME` automatically.
   * If `local.properties` is available, Gradle will use this file having a wrong `sdk.dir` set and fail.

## Update
I got some enquiry to provide a complete gradle file to understand where to do the pasting.
Here's one sample file:

        apply plugin: 'com.android.application'

        Properties properties = new Properties()
        properties.load(project.rootProject.file('local.properties').newDataInputStream())
        def sdkDir = properties.getProperty('sdk.dir')

        android {
            compileSdkVersion 26
            buildToolsVersion "26.0.1"
            defaultConfig {
                applicationId "com.pietromaggi.sample.barcode"
                minSdkVersion 19
                targetSdkVersion 22
                versionCode 1
                versionName "1.0"
                testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
            }
            buildTypes {
                release {
                    minifyEnabled false
                    proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
                }
            }
        }

        dependencies {
            compile fileTree(dir: 'libs', include: ['*.jar'])

            provided fileTree(include: ['com.symbol.emdk.jar'], dir: sdkDir+'/add-ons/addon-symbol_emdk-symbol-23/libs/')

            androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
                exclude group: 'com.android.support', module: 'support-annotations'
            })
            compile 'com.android.support:appcompat-v7:26.+'
            compile 'com.android.support.constraint:constraint-layout:1.0.2'
            testCompile 'junit:junit:4.12'
        }
