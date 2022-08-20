---
title: Getting device info from MSI Android devices
date: 2014-09-02T11:56:38+02:00
author: Pietro F. Maggi
summary: Obsolete - How to use the standard Android SDK to retrieve information about the device the app is running on.
keywords: [Android, Zebra Technologies, Sample]
tags: [Android, Zebra Technologies, Sample]
topics: [Android]
draft: false
type: post
---

# **2019 Update**

**The information in this post are obsolete. Please refer to the Android documentation for updates on this topic.**


# The good old ways!
On Symbol/MSI Windows Mobile devices, we where providing some APIs in the EMDK to get additional information.
Looking into the Resource Coordinator is possible to find APIs like:

  - RCM_GetESN() - retrieves the device electronic serial number
  - RCM_GetUniqueUnitId() - retrieves the unique unit identification number
  - RCM_GetVersion() - retrieves version information
  - etc...

...and we don't have anything like this in the Android EMDK. WHY?

Simply because the functionality is already included in the standard Android SDK.

Let's see how to get this is data on an MSI device with the Android OS.

# The fabulous new way!
You can find the completed project in [this repository](https://github.com/pfmaggi/GetDeviceInfo) on my github account, I'm using Windows 7 and Eclipse+ADT, but you can follow these steps with Android Studio quite nicely.

# Create a new Project
Create a new Android project, nothing fancy here, it's just a standard app with a single Blank Activity.
You can follow these images as a guideline. Your interface may vary as Google updates the Android wizard quite often.

 ![Create Project - step 1](/images/samples/device-info/create_project_01.jpg "Android New Project Wizard - page 1")
 ![Create Project - step 2](/images/samples/device-info/create_project_02.jpg "Android New Project Wizard - page 2")
 ![Create Project - step 3](/images/samples/device-info/create_project_03.jpg "Android New Project Wizard - page 3")
 ![Create Project - step 4](/images/samples/device-info/create_project_04.jpg "Android New Project Wizard - page 4")
 ![Create Project - step 5](/images/samples/device-info/create_project_05.jpg "Android New Project Wizard - page 5")

Then we can do some housekeeping deleting the unnecessary main.xml menu resource:

 ![Delete Menu resource](/images/samples/device-info/delete_menu.jpg "Delete unnecessary menu resource")

We can then start the two main changes:
  - Setting up the Activity Layout
  - Updating the Activity onCreate method to retrieve the device data

 ![Main Activity Layout](/images/samples/device-info/activity_layout.jpg "Main Activity Layout")

Here's the code:

```xml
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical"
    >
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/device_name"
        />
    <TextView
        android:id="@+id/device_type"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="16dp"
        android:layout_marginRight="16dp"
        android:textSize="18sp"
        style="?android:listSeparatorTextViewStyle"
        />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/electronic_serial_number"
        />
    <TextView
        android:id="@+id/device_esn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="16dp"
        android:layout_marginRight="16dp"
        android:textSize="18sp"
        style="?android:listSeparatorTextViewStyle"
        />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/build_number"
        />
    <TextView
        android:id="@+id/build_number"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="16dp"
        android:layout_marginRight="16dp"
        android:textSize="18sp"
        style="?android:listSeparatorTextViewStyle"
        />
</LinearLayout>
```

This together with the string.xml containing the referenced string:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <string name="app_name">Get Device Info</string>
    <string name="electronic_serial_number">Device ESN:</string>
    <string name="build_number">Build Number:</string>
    <string name="device_name">Device:</string>

</resources>
```


Then the simple Activity java code to collect the information is:

```java
package com.pietromaggi.sample.getdeviceinfo;

import android.os.Build;
import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.widget.TextView;


public class MainActivity extends ActionBarActivity {
    TextView DeviceNameTextView;
    TextView ESNTextView;
    TextView BuildNumberTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        DeviceNameTextView = (TextView)findViewById(R.id.device_type);
        DeviceNameTextView.setText(Build.DEVICE);

        ESNTextView = (TextView)findViewById(R.id.device_esn);
        ESNTextView.setText(Build.SERIAL);

        BuildNumberTextView = (TextView)findViewById(R.id.build_number);
        BuildNumberTextView.setText(Build.ID);
    }
}
```

# Where's the tricks?
There's really no trick, Android SDK provide this information, and more using these constants:

  - [Build.DEVICE = The name of the industrial design](http://developer.android.com/reference/android/os/Build.html#DEVICE)
  - [Build.SERIAL = A hardware serial number, if available](http://developer.android.com/reference/android/os/Build.html#SERIAL)
  - [Build.ID = changelist number](http://developer.android.com/reference/android/os/Build.html#ID)

Obviously, it's the OEM building the device that put together the plumbing to link the correct information. Your mileage may vary on different devices.

Running this application on an ET1 with Jelly Bean you get:

![ET1 Screenshot](/images/samples/device-info/screen_ET1.png "ET1 Screenshot")

Running this application on an MC40 with Jelly Bean you get:

![MC40 Screenshot](/images/samples/device-info/screen_MC40.png "MC40 Screenshot")

While the TC55 is always a bit different :-)

![TC55 Screenshot](/images/samples/device-info/screen_TC55JB.png "TC55 Screenshot")

Hope you may find this useful. Send me an email if you'd like to see any particular topic on this blog.
