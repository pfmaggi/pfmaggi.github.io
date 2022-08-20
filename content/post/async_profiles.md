---
title: "Zebra's EMDK Async Profile Manager APIs"
summary: "A step-by-step introduction to the Async version of the Zebra's EMDK for Android APIs to process Mx profiles."
date: 2018-06-26T09:00:00+02:00
type: "post"
draft: false
author: "Pietro F. Maggi"
tags: ["Android", "Zebra", "EMDK", "Profile Manager"]
---

*Note: [the accompanying code is available on github](https://github.com/Zebra/EMDKProfileAsync).*

Summer is the perfect time for some cleanup, and I've some older content laying around my laptop that is about time I publish!

I'm planning to cover some long-standing argument that I've touched over the last few years as Zebra's EMEA Software Consultant.

[Years ago we had some posts on the EMDK discussion forum about some instability seen if an application tries to apply a profile just after device boot.](https://developer.zebra.com/message/88398#88398)

This blog wants to be an in deep discussion about what is going on in these cases and what is the best approach to adopt in these cases.

## EMDK's Profile Manager API

This was the first API introduced in the EMDK v2.0 back in 2014. A little-known fact is that Zebra introduced an Async version of this API to cover a couple of use cases:

- Complex profiles that can lock take few seconds to be applied by Mx locking the calling task
- Need to apply the profile immediately after booting the device

Fast forwarding to 2016, Zebra released EMDK v4.0 where we can find as a new feature the Async version of the `getInstance()` method:

> Added new method `getInstanceAsync()` to EMDKManager. The EMDK Feature Manager object returned by this method is guaranteed to be usable immediately. The feature manager object returned by the existing method `getInstance()`, may not be ready to be used immediately, especially after a device reboot.

This, together with the `ProcessProfileAsync()` method allows building an application that process a profile asynchronously as soon as the Mx framework is available.

<span style="display:block;text-align:center">
![Async All the things](/images/20180628_async/async_all_the_things.png "Async")
</span>

## Setting the stage for the problem

As of today, the latest available [Zebra's EMDK for Android is v6.9](https://www.zebra.com/us/en/support-downloads/software/developer-tools/emdk-for-android.html) I'm going to use this version for this tutorial, together with AndroidStudio v3.1.3 on a Nougat TC56 with BSP 01.01.49 and LifeGuard patch 7.

The first step is to create a working application using the Synchronous version of the Profile Manager API to apply a simple profile, in this case, we can use the Clock profile, with this sample EMDKConfig.xml:

    <?xml version="1.0" encoding="UTF-8"?><!--This is an auto generated document. Changes to this document may cause incorrect behavior.--><wap-provisioningdoc>
    <characteristic type="ProfileInfo">
        <parm name="created_wizard_version" value="6.8.1"/>
    </characteristic>
    <characteristic type="Profile">
        <parm name="ProfileName" value="ClockProfile"/>
        <parm name="ModifiedDate" value="2018-06-26 12:36:23"/>
        <parm name="TargetSystemVersion" value="4.2"/>
        <characteristic type="Clock" version="4.2">
        <parm name="emdk_name" value=""/>
        <parm name="AutoTime" value="false"/>
        <parm name="TimeZone" value="GMT+02:00"/>
        <parm name="Date" value="2018-06-28"/>
        <parm name="Time" value="09:00:00"/>
        </characteristic>
    </characteristic>
    </wap-provisioningdoc>

You can find the demo at this stage of the [github project looking for the `sync` tag](https://github.com/Zebra/EMDKProfileAsync/releases/tag/sync).

This version of the application works as expected, you launch it, and it changes the date and time of the device.

<span style="display:block;text-align:center">
![Sync Success](/images/20180628_async/sync_success.png "sync Success")
</span>

**BUT**

If you run this application just after the device finish to boot you can get some strange errors:

<span style="display:block;text-align:center">
![Sync Error](/images/20180628_async/sync_error.png "Sync Error")
</span>

Because the error is time-dependent you can be lucky and see a message that provides a bit more insight into what is going on here:

<span style="display:block;text-align:center">
![Sync Initializing](/images/20180628_async/sync_initializing.png "Sync Initializing")
</span>

That's exactly the point, the Mx framework is not immediately ready after boot!

<span style="display:block;text-align:center">
[![I'm not ready!](/images/20180628_async/keep_calm_notready.png "I'm not ready!")](https://www.keepcalm-o-matic.co.uk/n/keep-calm-mx-is-not-ready-yet/)
</span>

## Enter the Async version of the APIs

As we wrote, there's a version of these API that works asynchronously providing two huge benefits:

1. The profile is processed on a different thread than the main (UI) thread. This avoid to freeze the app UI
2. It is executed as soon as the Mx framework is available

The first benefit can be achieved even with the synchronous APIs as shown in the [EMDK sample ProfileClockSample1](https://github.com/Zebra/samples-emdkforandroid-6_9/tree/master/ProfileClockSample1) running the ProcessProfile method on a thread different than the UI one. Note that the synchronous version of the sample implemented for this blog instead runs this method on the UI thread, potentially freezing the UI.

The second benefit is quite unique and, if you need to process a profile as soon as the device has booted, it could be a deal breaker.

### Which are the async APIs?

Let's see which are the APis we're talking about:

- [`void getInstanceAsync(EMDKManager.FEATURE_TYPE featureType, EMDKManager.StatusListener statusListener)`](https://techdocs.zebra.com/emdk-for-android/6-9/api/reference/com/symbol/emdk/EMDKManager.html#getInstanceAsync-com.symbol.emdk.EMDKManager.FEATURE_TYPE-com.symbol.emdk.EMDKManager.StatusListener-)
<br>
*This method is an asynchronous call and requests object instance for the specified feature type and object is returned through the status listener callback when the feature is initialized and ready to use.*
- [`EMDKResults processProfileAsync(String profileName, ProfileManager.PROFILE_FLAG profileFlag, String[] extraData)`](https://techdocs.zebra.com/emdk-for-android/6-9/api/reference/com/symbol/emdk/ProfileManager.html#processProfileAsync-java.lang.String-com.symbol.emdk.ProfileManager.PROFILE_FLAG-java.lang.String:A-)
<br>
*Processes the given profile based on the data provided and the flag and return status of the request. This is an asynchronous method and result will be returned through the registered callback.*

To use the `getInstanceAsync` we need to implement the `EMDKManager.StatusListener` interface, with an `onStatus` method.

the `onStatus` method is called when the EMDK is able to provide an instance of the object requested. In our case, a ProfileManager object. So, the first thing we can do is to check that we got the right object:

    if ((EMDKResults.STATUS_CODE.SUCCESS != statusData.getResult()) ||
        (EMDKManager.FEATURE_TYPE.PROFILE != statusData.getFeatureType()) ||
        (EMDKManager.FEATURE_TYPE.PROFILE != emdkBase.getType())){

        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                statusTextView.setText("Error: The Profile has not been sent for processing...");
            }
        });

        return;
    }

*Keep in mind that this code is not run on the UI Thread, this is why to change an element of the UI, we need to create a runnable on the right thread.*

After having checked that everything is OK, we can start to use our `ProfileManager` object:

    ProfileManager profileManager = (ProfileManager) emdkBase;

Now, before actually being able to process a profile asynchronously, we have to register a DataListener that is going to evaluate the result of the `ProcessProfileAsync` call:

    profileManager.addDataListener(new ProfileManager.DataListener() {
        @Override
        public void onData(ProfileManager.ResultData resultData) {
            String resultString = "Error Applying profile";
            EMDKResults results = resultData.getResult();

            //Check the return status of processProfile
            if (results.statusCode == EMDKResults.STATUS_CODE.SUCCESS) {
                resultString = "Profile Applied Successfully";
            } else if (results.statusCode == EMDKResults.STATUS_CODE.CHECK_XML) {
                resultString = parseXML(results.getStatusString());
            }

            final String finalResultString = resultString;
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    statusTextView.setText(finalResultString);
                }
            });
        }
    });

Keep in mind that this is a `ProfileManager.DataListener`, different from the DataListener used in the barcode API.

Once we have the DataListener in place, we can process the profile checking that the XML is going to be processed by Mx:

    EMDKResults results = profileManager.processProfileAsync(profileName,
            ProfileManager.PROFILE_FLAG.SET, modifyData);

    if (results.statusCode != EMDKResults.STATUS_CODE.PROCESSING) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                statusTextView.setText("Error: The Profile has not been sent for processing...");
            }
        });
    }

## Putting all the pieces together

In the end, putting all the pieces together, this is the code for the onStatus callback:

    @Override
    public void onStatus(EMDKManager.StatusData statusData, EMDKBase emdkBase) {
        if ((EMDKResults.STATUS_CODE.SUCCESS != statusData.getResult()) ||
                (EMDKManager.FEATURE_TYPE.PROFILE != statusData.getFeatureType()) ||
                (EMDKManager.FEATURE_TYPE.PROFILE != emdkBase.getType())){

            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    statusTextView.setText("Error: The Profile has not been sent for processing...");
                }
            });

            return;
        }

        ProfileManager profileManager = (ProfileManager) emdkBase;

        if (profileManager != null) {
            String[] modifyData = new String[1];

            profileManager.addDataListener(new ProfileManager.DataListener() {
                @Override
                public void onData(ProfileManager.ResultData resultData) {
                    String resultString = "Error Applying profile";
                    EMDKResults results = resultData.getResult();

                    //Check the return status of processProfile
                    if (results.statusCode == EMDKResults.STATUS_CODE.SUCCESS) {
                        resultString = "Profile Applied Successfully";
                    } else if (results.statusCode == EMDKResults.STATUS_CODE.CHECK_XML) {
                        resultString = parseXML(results.getStatusString());
                    }

                    final String finalResultString = resultString;
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            statusTextView.setText(finalResultString);
                        }
                    });
                }
            });

            EMDKResults results = profileManager.processProfileAsync(profileName,
                    ProfileManager.PROFILE_FLAG.SET, modifyData);

            if (results.statusCode != EMDKResults.STATUS_CODE.PROCESSING) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        statusTextView.setText("Error: The Profile has not been sent for processing...");
                    }
                });
            }
        }
    }

Now, if run this version of the application just after a device boot you'll see that the profile is correctly applied as soon as Mx is ready.

### **NOTE**

> This is a sample application that wants to show how to use these APIs with the minimum amount of ceremony, there's little or no error control and there's no management of configuration changes that could destroy and recreate the activity spawning a new call to process the profile!

The sample project can be downloaded from github, [this version is associated with the `async` tag](https://github.com/Zebra/EMDKProfileAsync/releases/tag/async)
