---
title: Managing Display AutoRotation from StageNow 
summary: Obsolete - Zebra Technologies' StageNow is a powerful tool that allows to easily stage a lot of the parameters required on COBO device. However we don't have a 100% coverage of all the Android Settings. Here I'll show how to build a minimal application that can be controlled from StageNow to set up the Display AutoRotation parameter. As this is not a reserved setting, any application can change this value. 
date: 2016-06-07T20:00:00+01:00
tags: [Android, Settings, StageNow, Zebra Technologies]
draft: false
author: Pietro F. Maggi
---

# **Edit:**
**Changes in API level 23 and newer make impossible for normal application to modify the autorotate setting.**

The application presented in this blog uses the `android.permission.WRITE_SETTINGS` that, starting from Android v6.0 Marshmallow (API level 23), requires the application to be *system or signed* to be able to run without user intervention.

There's a workaround that can be used targeting API level 22. In this way the application can still use the old permission model and it can write into the settings.

However, [because Google already announced that from August 2018 new application will need to target API level 26 to be accepted into the Play Store](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html), this is a short lived solution. It's only a viable solution if your application is never going to be included in the play store.

Anyway, [I've updated the code on github with this change](https://github.com/pfmaggi/autorotate) and uploaded [a new apk here](https://github.com/pfmaggi/autorotate/releases/download/v1.0.1/autorotate.apk).

**To handle correctly this permission in a normal application, user intervention is required.**

The application should use the `Settings.ACTION_MANAGE_WRITE_SETTINGS` intent with something like:

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        if (!Settings.System.canWrite(getApplicationContext())) {
            Intent intent = new Intent(Settings.ACTION_MANAGE_WRITE_SETTINGS, Uri.parse("package:" + getPackageName()));
            startActivity(intent);
            bDoIt = false;
        }
    }

# Follows original post

Another busy day with enquiry about our devices/tools.  
The funny thing is that, most of the time, answer to a question with another one is actually a good strategy :-)

This one cames from my colleague Raymond:

> Can we launch an adb commands from Stagenow?

The quick answer is no, the right answer is: _what are you trying to achieve?_ 

> I need a way to disable the Disaply AutoRotate setting, something that is possible to do using an `adb shell` command:

```
adb shell content insert --uri content://settings/system --bind name:s:accelerometer_rotation 
```

Ok, so the real question is:

> Can I disable Display AutoRotation using StageNow?

In this case the answer is yes, with some little help.
![Disable AutoRotate](/images/autorotate/disable.gif "Disable AutoRotate")

## The blueprint
The idea to implement this is to have an application on the device that can disable this setting...  
Ok, so not really ground-breaking. But wait, there's more.  
Instead of having an application that blindly disable this setting, we can build it so that we can launch it with different arguments and act on the arguments.  

To give you the complete picture, we can think that to implement the complete solution we need few steps in StageNow:

1. Install the application on the device
2. Launch the application with the proper argument (activate or deactivate AutoRotate in this case)
3. Uninstall the application (optional step)
 
### Writing to the settings
An Android application can update the settings if it has the permission:

```
android.permission.WRITE_SETTINGS
```

Just keep in mind that [this does not allow full access to the settings](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_SETTINGS), since Android v4.2.2, Google moved some of the settings under the _Secure Settings_ that requires a different permission that can be granted only to application that resides in the system folder or are signed with the platform keys. For additional information take a look at [`android.permission.WRITE_SECURE_SETTINGS`](https://developer.android.com/reference/android/Manifest.permission.html#WRITE_SECURE_SETTINGS).

Given that you application has the permission to write to the settings you can use the following code to write a new value to the AutoRotate setting:

    android.provider.Settings.System.putInt(getContentResolver(), Settings.System.ACCELEROMETER_ROTATION, value);

### Using Intent extra parameters

Ok, now that we've a way to set the settings, we want to have a way to get a parameter from StageNow to decide if we've to enable or disable the Display AutoRotate setting.  
To do this we can use some extra parameters in the Intent that is used to launch our application:


    private final static String KEY_ROTATION = "AutoRotate";

    Bundle extras = getIntent().getExtras();
    if (extras != null) {
        value = extras.getInt(KEY_ROTATION);
    }

Now, from the command line, `adb shell` to send the right parameter to our sample app:

    adb shell am start -n com.pietromaggi.sample.autorotate/.MainActivity --ei "AutoRotate" 0 

## Putting the different pieces together
To keep things simple, I've build a very simple demo application that implement these two features, and then closes the activity.  
you can find the source code [in this github repository](https://github.com/pfmaggi/autorotate) and the [final release apk here](https://github.com/pfmaggi/autorotate/releases/download/v1.0.0/autorotate.apk).

This on the device, we now need to put together the StageNow profile that install the apk and then send the intent to our application.

To create the StageNow profile use the Expert Mode and add these steps:

### 1. File Manager - to copy the apk on the device  

![Copy AutoRotate apk on the device](/images/autorotate/StageNow_1.png "Copy AutoRotate apk on the device")

### 2. App Manager - to install/update the apk

![Install/update the apk](/images/autorotate/StageNow_2.png "Install/update the apk")

### 3. Intent Manager - to launch the autorotate app with the disable parameter

![launch the autorotate app with the disable parameter](/images/autorotate/StageNow_3.png "launch the autorotate app with the disable parameter")

### 4. App Manager - uninstall the apk

![uninstall the apk](/images/autorotate/StageNow_4.png "uninstall the apk")



## Testing
Once we've created the StageNow profile, it's simply a matter of putting the device on the same network of the StageNow server and scan the two barcodes.  
The StageNow client on the device will start downloading the apk, installing it, etc. reporting a staging succesful message at the end... hopefully :-)

## Where to go from here
A natural evolution of this technique would be to add more settings handling to this sample app, assigning a different Intent Extra to every parameter, so we can use a single application to manage multiple settings.

Let me know if you've any comment/request about this topic.



