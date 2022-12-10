---
title: Getting device info from MSI Android devices
date: 2014-09-02
author: Pietro F. Maggi
summary: Obsolete - How to use the standard Android SDK to retrieve information about the device the app is running on.
categories:
    - "Android"
    - "Zebra Technologies"
    - "Sample"
toc: true
---


{{< div class="danger pure-button" >}}
> The information in this article is obsolete.<br>Please refer to the Android documentation for updates on this topic
{{<div "end" >}}

# The good old ways!
On Symbol/MSI Windows Mobile devices, we where providing some APIs in the EMDK to get additional information.
Looking into the Resource Coordinator is possible to find APIs like:

  - `RCM_GetESN()` - retrieves the device electronic serial number
  - `RCM_GetUniqueUnitId()` - retrieves the unique unit identification number
  - `RCM_GetVersion()` - retrieves version information
  - etc...

...and we don't have anything like this in the Android EMDK. WHY?

Simply because the functionality is already included in the standard Android SDK.

Let's see how to get this is data on an MSI device with the Android OS.

# The fabulous new way!
You can find the completed project in [this repository](https://github.com/pfmaggi/GetDeviceInfo) on my github account, I'm using Windows 7 and Eclipse+ADT, but you can follow these steps with Android Studio quite nicely.

# Create a new Project
Create a new Android project, nothing fancy here, it's just a standard app with a single Blank Activity.
You can follow these images as a guideline. Your interface may vary as Google updates the Android wizard quite often.


{{< figure
  src="/images/samples/device-info/create_project_01.jpg"
  class="class param"
  title="Android New Project Wizard - page 1"
  label="mn-export-import"
  alt="Android New Project Wizard - page 1"
 >}}
{{< section "end" >}}

{{< figure
  src="/images/samples/device-info/create_project_02.jpg"
  class="class param"
  title="Android New Project Wizard - page 2"
  label="mn-export-import"
  alt="Android New Project Wizard - page 2"
 >}}
{{< section "end" >}}

{{< figure
  src="/images/samples/device-info/create_project_03.jpg"
  class="class param"
  title="Android New Project Wizard - page 3"
  label="mn-export-import"
  alt="Android New Project Wizard - page 3"
 >}}
{{< section "end" >}}

{{< figure
  src="/images/samples/device-info/create_project_04.jpg"
  class="class param"
  title="Android New Project Wizard - page 4"
  label="mn-export-import"
  alt="Android New Project Wizard - page 4"
 >}}
{{< section "end" >}}

{{< figure
  src="/images/samples/device-info/create_project_05.jpg"
  class="class param"
  title="Android New Project Wizard - page 5"
  label="mn-export-import"
  alt="Android New Project Wizard - page 5"
 >}}
{{< section "end" >}}

Then we can do some housekeeping deleting the unnecessary main.xml menu resource:

{{< figure
  src="/images/samples/device-info/delete_menu.jpg"
  class="class param"
  title="Delete unnecessary menu"
  label="mn-export-import"
  alt="Delete unnecessary menu"
 >}}
{{< section "end" >}}

We can then start the two main changes:
  - Setting up the Activity Layout
  - Updating the Activity onCreate method to retrieve the device data

{{< figure
  src="/images/samples/device-info/activity_layout.jpg"
  class="class param"
  title="Main Activity Layout"
  label="mn-export-import"
  alt="Main Activity Layout"
 >}}
{{< section "end" >}}

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

## Where's the tricks?
There's really no trick, Android SDK provide this information, and more using these constants:

  - [`Build.DEVICE`][1] = The name of the industrial design
  - [`Build.SERIAL`][2] = A hardware serial number, if available 
  - [`Build.ID`][3] = changelist number

Obviously, it's the OEM building the device that put together the plumbing to link the correct information. Your mileage may vary on different devices.

Running this application on an ET1 with Jelly Bean you get:

{{< figure
  src="/images/samples/device-info/screen_ET1.png"
  class="class param"
  title="ET1 Screenshot"
  label="mn-export-import"
  alt="ET1 Screenshot"
 >}}
{{< section "end" >}}

Running this application on an MC40 with Jelly Bean you get:

{{< figure
  src="/images/samples/device-info/screen_MC40.png"
  class="class param"
  title="MC40 Screenshot"
  label="mn-export-import"
  alt="MC40 Screenshot"
 >}}
{{< section "end" >}}

While the TC55 is always a bit different :-)

{{< figure
  src="/images/samples/device-info/screen_TC55JB.png"
  class="class param"
  title="TC55 Screenshot"
  label="mn-export-import"
  alt="TC55 Screenshot"
 >}}
{{< section "end" >}}

Hope you may find this useful. Send me an email if you'd like to see any particular topic on this blog.

[1]: http://developer.android.com/reference/android/os/Build.html#DEVICE
[2]: http://developer.android.com/reference/android/os/Build.html#SERIAL
[3]: http://developer.android.com/reference/android/os/Build.html#ID
