---
title: Disabling MC40 Home Button with a custom intent
summary: MC40 with Android v4.1 and v4.4 includes a feature in the OS that allows to disable the Home Button through an Intent.
date: 2016-01-18T09:00:00+01:00
tags: [Android, MC40, Tricks, Sample, Zebra Technologies]
draft: false
author: Pietro F. Maggi
---

# Note:
_[I've published another post that explain how to disable the Home Button on all the Zebra's Android devices that are supported by Mx v4.3+.]({{ relref . "post/mx_home_button.md" }})_

## Intro
Zebra Technologies' devices are purpose built to be single use devices. Having an OS like Android nowadays provides a lot of flexibility...  sometimes a bit too much.  
With this idea in mind, the Android version used on these devices sometimes provide some nice surprise, like an Intent to disable the omnipresent Android's Home button.

## The basic idea
MC40 support an [Android intent](http://developer.android.com/reference/android/content/Intent.html) to disable the normal Home button behavior: *You can disable it and there's no way to exit from the foreground application* [^1].

[You can find a blog by Pavel Machala briefly describing the idea on Launchpad](https://developer.zebra.com/community/android/android-forums/android-blogs/blog/2014/12/19/disabling-home-button-via-intent-mc40).  
Here I want to present a simple sample that show how to use this intent, covering the only issue that is: *handling re-branded devices*

## Project setup
We start creating a new Android application in Android Studio (I'm using Android Studio v1.5.1 with Build tools 23.0.1, you're mileage can vary if you're using a different setup).  

*Note:* is not required to build this application using Zebra Technologies' EMDK. A normal Android application can use this intent.

![Creating a new Android Application, step 1](/images/samples/mc40_home_btn/AndroidNewPrj_01.jpg "Creating a new Android Application, step 1")
![Creating a new Android Application, step 2](/images/samples/mc40_home_btn/AndroidNewPrj_02.jpg "Creating a new Android Application, step 2")
![Creating a new Android Application, step 3](/images/samples/mc40_home_btn/AndroidNewPrj_03.jpg "Creating a new Android Application, step 3")
![Creating a new Android Application, step 4](/images/samples/mc40_home_btn/AndroidNewPrj_04.jpg "Creating a new Android Application, step 4")

Once we've the app framework setup by Android Studio, we can simplify the interface down to a Status message and a button.  
First step: remove `content_main.xml` and `menu_main` then replace the content of `activity_main.xml` with:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">
                    
    <TextView
        android:id="@+id/tvwHomeBtnStatus"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" 
        android:padding="24dp"
        android:text="@string/home_btn_status_on"/>

    <Button
        android:id="@+id/btnToggleStatus"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" 
        android:text="@string/toggle"/>
</LinearLayout>
```

This layout requires the following `string.xml` inside the `res/values` folder:

```xml
<resources>
    <string name="app_name">MC40HomeButton</string>
    <string name="home_btn_status_on">Home Button Enabled</string>
    <string name="home_btn_status_off">Home Button Disabled</string>
    <string name="toggle">Toggle current device setting</string>
</resources>
```
![Our Strings.xml at this point](/images/samples/mc40_home_btn/Strings.jpg "Our Strings.xml at this point")

We can now remove a lot of the generated template code in `MainActivity.java` trimming down the code to:

```java
package com.pietromaggi.sample.mc40homebutton;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}                                
```

If we run the application at this moment, it's not doing a lot :-)

![MC40 Screenshot of the app as recorded at tag1](/images/samples/mc40_home_btn/Screenshot_tag1.png "MC40 Screenshot of the app as recorded at tag1")

You can find the app at this stage [on the github repository under the `tag1` tag](https://github.com/pfmaggi/mc40_homebutton/tree/tag1).

Next step would be to check that we're really running on a Zebra Technologies' MC40, and then adding some logic to the app. 

## Dealing with multiple OS versions and re-branding

First we check that we're running on a Zebra's MC40 using a couple of Android constant:

  - `android.os.Build.MANUFACTURER`   
  - `android.os.Build.MODEL`   

The catch is that MC40, running Jelly Bean, report "Motorola Solution" as `MANUFACTURER`, we need to address this difference and, as reported in [Pavel's notes](https://developer.zebra.com/community/android/android-forums/android-blogs/blog/2014/12/19/disabling-home-button-via-intent-mc40), the broadcast intent action is different for Motorola Solution or Zebra's MC40.

Once we've seen if the device the app is running on is supported, we can send a first Intent, to disable the home button, and configure the toggle button, so that is really toggle the Home Button status from disable to enabled and vice versa.

```
package com.pietromaggi.sample.mc40homebutton;

import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {
  private static final String MANUFACTURER_ZEBRA = "Zebra Technologies";
  private static final String MANUFACTURER_MSI = "Motorola Solutions";
  private static final String MODEL_MC40 = "MC40N0";
  private static final String INTENT_ZEBRA = "com.symbol.intent.action.HOMEKEY_MODE";
  private static final String INTENT_MSI = "com.motorolasolutions.intent.action.HOMEKEY_MODE";
  private static final String INTENT_EXTRA = "state";
  private String mStrIntent;
  private TextView mTvwStatus;
  private int mStatus;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    String StrManufacturer = Build.MANUFACTURER;
    String strModel = Build.MODEL;
    boolean bSupported = true;

    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    mTvwStatus = (TextView) findViewById(R.id.tvwHomeBtnStatus);

    if (strModel.equalsIgnoreCase(MODEL_MC40)) {
      // We're running on an MC40
      if (StrManufacturer.equalsIgnoreCase(MANUFACTURER_ZEBRA)) {
        // This is a Zebra Device
        mStrIntent = INTENT_ZEBRA;
      } else if (StrManufacturer.equalsIgnoreCase(MANUFACTURER_MSI)) {
        // This is a Motorola Solution Device
        // just double OS version, only Jelly Bean is supported
        mStrIntent = INTENT_MSI;
        if (Build.VERSION_CODES.JELLY_BEAN != Build.VERSION.SDK_INT) {
          bSupported = false;
        }

      }
    } else {
      // This is something else, not supported
      bSupported = false;
    }

    Button btnToggle = (Button)findViewById(R.id.btnToggleStatus);
    if (bSupported) {
      mTvwStatus.setText(R.string.home_btn_status_off);
      mStatus = 0;
      Intent i = new Intent(mStrIntent);
      i.putExtra(INTENT_EXTRA, mStatus);
      sendBroadcast(i);

      btnToggle.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View v) {
          //Toggle Current Status
          if (0 == mStatus) {
            mStatus = 1;
            mTvwStatus.setText(R.string.home_btn_status_on);
          } else {
            mStatus = 0;
            mTvwStatus.setText(R.string.home_btn_status_off);
          }
          Intent i = new Intent(mStrIntent);
          i.putExtra(INTENT_EXTRA, mStatus);
          sendBroadcast(i);
        }
      });
    } else {
      mTvwStatus.setText(R.string.device_not_supported);
      btnToggle.setEnabled(false);
    }
  }
}
```

If we run the application on a supported device, we get the expected behavior:

![MC40 Screenshot of the app as recorded at tag2](/images/samples/mc40_home_btn/Screenshot_tag2.png "MC40 Screenshot of the app as recorded at tag2")

## Managing state
The application now works as expected, but with an issue linked to not managing the state. As an example, simply rotating the device, the Activity is destroyed to create a new one with the standard behavior to disable the Home Button, even if we just re-enabled it using the `toggle` button.  
An easy solution is to add some code to manage the Application status overriding the `onSaveInstanceState(Bundle)` method:

```
@Override
public void onSaveInstanceState(Bundle savedInstanceState) {
  super.onSaveInstanceState(savedInstanceState);
  savedInstanceState.putInt(KEY_STATE, mState);
}
```

And update the [`onCreate` method to check the bundle to see if it's defined and behave accordingly](https://github.com/pfmaggi/mc40_homebutton/blob/tag3/app/src/main/java/com/pietromaggi/sample/mc40homebutton/MainActivity.java#L55).  
The final version of the application, managing the state, can be find on github associated to [tag3](https://github.com/pfmaggi/mc40_homebutton/tree/tag3).

Let me know if you've found this article useful.

----
##### Notes

[^1]: If your app locks down a few other things or you're using something like Zebra's [Enterprise Home Screen](https://www.zebra.com/us/en/products/software/mobile-computers/mobile-app-utilities/enterprise-home-screen.html).
