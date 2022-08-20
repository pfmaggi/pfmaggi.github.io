---
title: Disabling Home Button on Zebra Android devices
summary: Mx v4.3 introduce includes a feature in the OS that allows to disable the Home Button using Mx through the EMDK or StageNow.
date: 2016-08-01T17:00:00+02:00
tags: [Android, Mx, Tricks, Sample, Zebra Technologies]
draft: false
author: Pietro F. Maggi
---

## Intro
I presented in the past a method to disable the Home Button on Zebra's MC40 Android device, using a custom intent[^1].
After having published it, I got some enquiries, about having the same behaviour on other Zebra's devices. Well, it turns out that there's now a "standard" way to do this in Zebra's Mx extension using the UI Manager profile. This post is going to show how to implement a simple application that can disable the Home Button on all Zebra's devices that have Mx v4.3 installed[^2].

## The basic idea
Mx v4.3 introduced the support in the [UI Manager to disable the Home Button](http://techdocs.zebra.com/emdk-for-android/4-2/mx/uimgr/#homekey-enabledisable) to disable the normal Home button behavior: *You can disable it and there's no way to exit from the foreground application*.

## Project setup
We start creating a new Android application in Android Studio. I'm using Android Studio v2.1.2 with Build tools 23.0.3, and the EMDK v4.2. Your mileage can vary if you're using a different setup.

*Note:* I prefer to integrate Zebra Technologies' EMDK as a library in the gradle module file. You can find more information about this in the [EMDK documentation](http://techdocs.zebra.com/emdk-for-android/4-2/tutorial/tutCreateProjectAndroidStudio/#emdkasadependencyingradlebuild) and in [this blog]({{ relref . "emdk_and_android_sdk.md" }}).

### Create a new Android project
![Creating a new Android Application, step 1](/images/samples/mx_home_btn/AndroidNewPrj_01.png "Creating a new Android Application, step 1")
![Creating a new Android Application, step 2](/images/samples/mx_home_btn/AndroidNewPrj_02.png "Creating a new Android Application, step 2")
![Creating a new Android Application, step 3](/images/samples/mx_home_btn/AndroidNewPrj_03.png "Creating a new Android Application, step 3")
![Creating a new Android Application, step 4](/images/samples/mx_home_btn/AndroidNewPrj_04.png "Creating a new Android Application, step 4")

### Add the EMDK library reference to the project
Next step is to add a reference to the EMDK library that you need to have already installed on your development machine [following the Getting Started guide](http://techdocs.zebra.com/emdk-for-android/4-2/guide/setup/).  In my case I'm using a mac and I need to modify my gradle file as:

    apply plugin: 'com.android.application'

    android {
        compileSdkVersion 23
        buildToolsVersion "23.0.3"

        defaultConfig {
            applicationId "com.pietromaggi.sample.mxhomebutton"
            minSdkVersion 16
            targetSdkVersion 23
            versionCode 1
            versionName "1.0"
        }
        buildTypes {
            release {
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }
    }

    dependencies {
        provided fileTree(include: ['com.symbol.emdk.jar'], dir: '/Applications/androidSDK/add-ons/addon-symbol-emdk_v4.2-API-16/libs/')
        compile fileTree(exclude: ['com.symbol.emdk.jar'], dir: 'libs')
        testCompile 'junit:junit:4.12'
        compile 'com.android.support:appcompat-v7:23.4.0'
    }

### Add the EMDK library and permission reference to the Android Manifest XML file
Next we need to update the AndroidManifest.xml file to add the requirement of having the EMDK library and requesting the EMDK permission:

    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.pietromaggi.sample.mxhomebutton">

        <uses-permission android:name="com.symbol.emdk.permission.EMDK" />

        <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:supportsRtl="true"
            android:theme="@style/AppTheme">

            <uses-library android:name="com.symbol.emdk" />

            <activity android:name=".MainActivity">
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />

                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
        </application>

    </manifest>

### Create the Mx Profile to disable the Home Button
We can use EMDK's Wizard to create the

![Create EMDK UIManager Profile, step 1](/images/samples/mx_home_btn/EMDKWizard_01.png "Create EMDK UIManager Profile, step 1")
![Create EMDK UIManager Profile, step 2](/images/samples/mx_home_btn/EMDKWizard_02.png "Create EMDK UIManager Profile, step 2")
![Create EMDK UIManager Profile, step 3](/images/samples/mx_home_btn/EMDKWizard_03.png "Create EMDK UIManager Profile, step 3")
![Create EMDK UIManager Profile, step 4](/images/samples/mx_home_btn/EMDKWizard_04.png "Create EMDK UIManager Profile, step 4")

In this way we obtain this profile to disable the home button:

    <?xml version="1.0" encoding="UTF-8"?>
    <!--This is an auto generated document. Changes to this document may cause incorrect behavior.-->
    <wap-provisioningdoc>
        <characteristic type="ProfileInfo">
            <parm name="created_wizard_version" value="4.1.1"/>
        </characteristic>
        <characteristic type="Profile">
            <parm name="ProfileName" value="HomeButton"/>
            <parm name="ModifiedDate" value="2016-08-01 14:12:10"/>
            <parm name="TargetSystemVersion" value="4.4"/>
            <characteristic type="UiMgr" version="4.3">
            <parm name="emdk_name" value=""/>
            <parm name="HomeKeyUsage" value="2"/>
            </characteristic>
        </characteristic>
    </wap-provisioningdoc>



### Add handling of EMDK Profile APIs in MainActivity
Here we can follow along EMDK documentation's [Data Capture Profile Feature Tutorial](http://techdocs.zebra.com/emdk-for-android/4-2/tutorial/tutdatacaptureprofile/).
First step is to implement the EMDKListener interface in the MainActivity class, obtaining this code:

    package com.pietromaggi.sample.mxhomebutton;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;

    import com.symbol.emdk.EMDKManager;

    public class MainActivity extends AppCompatActivity implements EMDKManager.EMDKListener {

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
        }

        @Override
        public void onOpened(EMDKManager emdkManager) {

        }

        @Override
        public void onClosed() {

        }
    }

We've now to request an EMDKMAnager Instance and, in the `onOpened` callback, get an instance of the Profile Manager to process the profile we created at the previous step.

Again, this is just a cut and past from the [Data Capture Profile Feature Tutorial](http://techdocs.zebra.com/emdk-for-android/4-2/tutorial/tutdatacaptureprofile/):

    package com.pietromaggi.sample.mxhomebutton;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.widget.Toast;

    import com.symbol.emdk.EMDKManager;
    import com.symbol.emdk.EMDKResults;
    import com.symbol.emdk.ProfileManager;

    public class MainActivity extends AppCompatActivity implements EMDKManager.EMDKListener {

        private EMDKManager emdkManager;
        private ProfileManager mProfileManager;
        private String profileName = "HomeButton";

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            //The EMDKManager object will be created and returned in the callback.
            EMDKResults results = EMDKManager.getEMDKManager(getApplicationContext(), this);

            //Check the return status of getEMDKManager
            if(results.statusCode != EMDKResults.STATUS_CODE.SUCCESS)
            {
                //Failed to create EMDKManager object
                Toast.makeText(this, "Error retrieving EMDK Manager instance.", Toast.LENGTH_SHORT);
            }
        }

        @Override
        public void onOpened(EMDKManager emdkManager) {
            this.emdkManager = emdkManager;
            //Get the ProfileManager object to process the profiles
            mProfileManager = (ProfileManager) emdkManager.getInstance(EMDKManager.FEATURE_TYPE.PROFILE);

            if(mProfileManager != null)
            {
                try{

                    String[] modifyData = new String[1];
                    //Call processProfile with profile name and SET flag to create the profile. The modifyData can be null.

                    EMDKResults results = mProfileManager.processProfile(profileName, ProfileManager.PROFILE_FLAG.SET, modifyData);
                    if(results.statusCode == EMDKResults.STATUS_CODE.FAILURE)
                    {
                        //Failed to set profile
                    }
                }catch (Exception ex){
                    // Handle any exception
                }


            }
        }

        @Override
        public void onClosed() {

        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            //Clean up the objects created by EMDK manager
            emdkManager.release();
        }
    }

    }

If we run this application on a Zebra device with Mx v4.3+ we see that it disables the Home Button.
Notes that this is not a kiosk mode, settings and notifications are still available and you need to use other Mx Profiles to disable these.  However, this setting is now persisted on the device even if you reset it.  To revert back to the default you need to do an *Enterprise Reset* or a *Factory Reset*.

### Add a small interface to enable/disable the home button
To complete this sample could be useful to add a small UI that allows to enable/disable the home button; something like:

![Sample UI with Toggle Button](/images/samples/mx_home_btn/AppUI.png "Sample UI with Toggle Button")

This is the new layout file:

    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:paddingBottom="@dimen/activity_vertical_margin"
        android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        tools:context="com.pietromaggi.sample.mxhomebutton.MainActivity">

        <LinearLayout
            android:orientation="horizontal"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_centerHorizontal="true">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/home_button_status"
                android:layout_centerHorizontal="true" />

            <ToggleButton
                android:id="@+id/tgHomeButton"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textOn="Enabled"
                android:textOff="Disabled" />


        </LinearLayout>
    </RelativeLayout>


And this is the code taking care of the click event for the toggle button. You can find here the whole onCreate callback:

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //The EMDKManager object will be created and returned in the callback.
        EMDKResults results = EMDKManager.getEMDKManager(getApplicationContext(), this);

        final ToggleButton tgHomeButton = (ToggleButton)findViewById(R.id.tgHomeButton);

        //Check the return status of getEMDKManager
        if(results.statusCode != EMDKResults.STATUS_CODE.SUCCESS)
        {
            //Failed to create EMDKManager object
            Toast.makeText(this, "Error retrieving EMDK Manager instance.", Toast.LENGTH_SHORT);
            tgHomeButton.setEnabled(false);
        } else {
            tgHomeButton.setEnabled(true);

            tgHomeButton.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    if(mProfileManager != null)
                    {
                        try{

                            String[] modifyData = new String[1];

                            boolean on = ((ToggleButton) v).isChecked();
                                modifyData[0] =
                                        "<?xml version=\"1.0\" encoding=\"UTF-8\"?><!--This is an auto generated document. Changes to this document may cause incorrect behavior.--><wap-provisioningdoc>\n" +
                                        "  <characteristic type=\"ProfileInfo\">\n" +
                                        "    <parm name=\"created_wizard_version\" value=\"4.1.1\"/>\n" +
                                        "  </characteristic>\n" +
                                        "  <characteristic type=\"Profile\">\n" +
                                        "    <parm name=\"ProfileName\" value=\"HomeButton\"/>\n" +
                                        "    <parm name=\"ModifiedDate\" value=\"2016-08-01 16:30:10\"/>\n" +
                                        "    <parm name=\"TargetSystemVersion\" value=\"4.4\"/>\n" +
                                        "    <characteristic type=\"UiMgr\" version=\"4.3\">\n" +
                                        "      <parm name=\"emdk_name\" value=\"\"/>\n" +
                                        "      <parm name=\"HomeKeyUsage\" value=\"" + (on?"1":"2") + "\"/>\n" +
                                        "    </characteristic>\n" +
                                        "  </characteristic>\n" +
                                        "</wap-provisioningdoc>\n";

                            //Call processProfile with profile name and SET flag to create the profile. The modifyData can be null.
                            EMDKResults results = mProfileManager.processProfile(profileName, ProfileManager.PROFILE_FLAG.SET, modifyData);
                            if(results.statusCode == EMDKResults.STATUS_CODE.FAILURE)
                            {
                                //Failed to set profile
                                Toast.makeText(MainActivity.this, "Failed to set the new profile", Toast.LENGTH_SHORT).show();
                            } else if (results.statusCode == EMDKResults.STATUS_CODE.CHECK_XML) {
                                String responseXML = results.getStatusString();
                                if (responseXML.contains("error")) {
                                    Toast.makeText(MainActivity.this, "Failed to set the new profile", Toast.LENGTH_SHORT).show();
                                }
                            }
                        }catch (Exception ex){
                            // Handle any exception
                            Toast.makeText(MainActivity.this, "Failed to set the new profile", Toast.LENGTH_SHORT).show();
                        }


                    }
                }

            });
        }
    }

### Conclusion
Mx is getting better and better, and the latest version have introduced some interesting additional features and more are going to be added in the future.
I hope that this small blog has made this clear and you can find more interesting suggestion on EMDK official documentation.


For now I leave you with a video of the app working and the complete source code of the project available on [github](https://github.com/pfmaggi/MxHomeButton).

![Sample UI with Toggle Button](/images/samples/mx_home_btn/AppUI.gif "Sample UI with Toggle Button")


[^1]: [Disabling MC40 Home Button with a custom intent]({{ relref . "mc40_home_button.md" }})
[^2]: [UI Manager documentation](http://techdocs.zebra.com/emdk-for-android/4-2/mx/uimgr/#homekey-enabledisable)
