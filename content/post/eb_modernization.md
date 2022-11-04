---
title: "Enterprise Browser - Modernizing web applications"
summary: "Enterprise Browser allows to create shortcuts to different `Config.xml` so the user can access them using a different icon on your Android Launcher screen. This is a step-by-step guide on how to create, test and distribute them."
date: 2018-03-21T21:30:48+01:00
draft: false
---

## *Note:* 

This blog describe the content presented during Zebra Technologies' DEVTALK recorded March 21st 2018 about Enterprise Browser. [You can find a recording of the presentation on Zebra's Developer Portal](https://developer.zebra.com/community/home/blog/2018/03/28/devtalk-l-wednesday-march-21-2018-l-1000-am-cdt-l-enterprise-browser-enable-your-web-application).

## Modernize Web Application

> Enterprise Browser is not just a browser, it's a business enabler that Symbol, Motorola, Motorola Solutions and now Zebra has developed over the last 15 years.

![Believe!](/images/20180321_eb_modernization/cat_believe.png "Believe!")

I know that fancy tag line remembers those cats' posters: but it's true!

## What is Enterprise Browser?

[Stealing from the official website](https://www.zebra.com/it/it/products/software/mobile-computers/mobile-app-utilities/enterprise-browser.html), Enterprise Browser (EB from now on) allows to use standard web technologies (HTML5, CSS and JavaScript) to build application that can fully exploit the hardware features of Zebra Technologies' devices, across multiple operative systems:

* Android
* Windows CE
* Windows Mobile

### How can you exploit HW capabilities like Barcode scanners?

Enterprise Browser makes available a set of proprietary APIs that allows direct control of barcode scanner, RFID, connected printers, camera, etc.

But this is only half of the story for Enterprise Browser, another good set of functionalities derives from it's configuration capabilities through it's [`config.xml`](https://techdocs.zebra.com/enterprise-browser/1-8/guide/configreference/).  
This is the file where you select the URL that is going to be used by the browser, but this is just the start. You can lock down a device, so that EB is running in Kiosk mode (both on legacy windows and Android); You can inject new functionality to an existing Web Application, without the need to modify anything on the server side (it's not magic, it's what we call [DOM Injection](http://techdocs.zebra.com/enterprise-browser/1-8/guide/DOMinjection/)) or isaply a fully customizable on-screen keyboard when using EB on an Android full-touch device.

## How we end-up here?

If we look at this product history we can see that it's not only about companies with different names. The product itself has been through multiple phases:

  1. Symbol PocketBrowser
  2. Motorola PocketBrowser
  3. Motorola Project Neon / RhoElements v1.x
  4. Motorola RhoElements v2.x Shared Runtime
  5. Motorola RhoElements v4.x Common API
  6. Zebra Enterprise Browser

Looking back at this product history should not surprise you that, over the last 15 years, we spent time adding features to support customers request. The end result of this activities is that the same goal can be achieved using different API or configuration settings.

### EMML 1.x - Enterprise Mashup Markup Language

EMML is the Enterprise MetaTags Markup Language used initially by Pocket Browser to add capabilities to web application and it evolved from the initial syntax in PB v1.x and 2.x to what is now used in EB (EMML1.1):

PB v2.x syntax to scan a barcode:

{{< highlight html "linenos=table" >}}
<HTML>
    <HEAD>
        <Meta http-equiv="scanner" content="AIM_TYPE_PRESENTATION">
        <Meta http-equiv="scannernavigate" content="Javascript:doScan('%s');">
        <Meta http-equiv="scanner" content="enabled">
    </HEAD>
    <BODY onLoad="doSoftScan();">
        <SCRIPT LANGUAGE="JavaScript">
            var Generic = new ActiveXObject("SymbolBrowser.Generic");

            function doSoftScan()
            {
                Generic.InvokeMetaFunction('scanner', 'start');
            }

            function doScan(data)
            {
                bcode.innerHTML = data;
                doSoftScan();
            }
        </SCRIPT>
        <div id="bcode"></div>
    </BODY>
</HTML>
{{< / highlight >}}

If we look at the same functionality in RhoElements v2.x Shared runtime, the syntax to scan a barcode using MetaTags and the `Scanner` API is not so different:

{{< highlight html "linenos=table" >}}
<HTML>
    <HEAD>
        <Meta http-equiv="scanner" content="aimType:presentation">
        <Meta http-equiv="scanner" content="DecodeEvent:url('Javascript:doScan('%s');')">
        <Meta http-equiv="scanner" content="enable">
    </HEAD>
    <BODY onLoad="doSoftScan();">
        <SCRIPT LANGUAGE="JavaScript">

            function doSoftScan() {
                scanner.start();
            }

            function doScan(jsonObject) {
                bcode.innerHTML = jsonObject.data;
                doSoftScan();
            }
        </SCRIPT>
        <div id="bcode"></div>
    </BODY>
</HTML>
{{< / highlight >}}

### RhoElements v4.x and the Common API in JavaScript (and Ruby)

RhoElements v4.0 introduced the concept of a *CommonAPI*, one true API to overcome all the differences between the original EMML syntax, RhoElements v1.x Syntax and the RhoMobile framework syntax:

> Three Rings for the Elven-kings under the sky,  
Seven for the Dwarf-lords in their halls of stone,  
Nine for Mortal Men doomed to die,  
One for the Dark Lord on his dark throne  
In the Land of Mordor where the Shadows lie.  
One Ring to rule them all, One Ring to find them,  
One Ring to bring them all and in the darkness bind them  
In the Land of Mordor where the Shadows lie.

The CommonAPI architecture is much more JavaScript and DOM friendly, adding only a single object:

* `Rho` - in the original RhoMobile v4.x syntax
* `EB` - in the current Enterprise Browser syntax

So, nowadays, all the new CommonAPIs are available under the `EB` object.  
To scan a barcode, the syntax now became:

{{< highlight html "linenos=table" >}}
    <html>
    <head>
        <title>Barcode API Test</title>
        <script type="text/javascript" charset="utf-8" src="ebapi-modules.js"></script>

        <script type="text/javascript">
            function scanReceived(params){
                // No data or no timestamp, scan failed.
                if(params['data']== "" || params['time']==""){
                    document.getElementById('display').innerHTML = "Failed!";
                    return;
                }
                // Data and timestamp exist, barcode successful, show results
                var displayStr = "Barcode Data: " + params['data']+"<br>Time: "+params['time'];
                document.getElementById("display").innerHTML = displayStr;
            }

            function enableScanners(){
                EB.Barcode.enable({}, scanReceived);
                // Empty property hash, '{}' loads default values for the scanner.
            }

            function unloadEvent(){
                EB.Barcode.disable();
                // Disable Barcode on unload of page to free it up for other operations.
            }
        </script>
        </head>

        <body onunload='unloadEvent()'>
            <h1>Barcode API Test</h1>
            <div id="display">
                Barcode Data: <br>
                Time: <br>
            </div>
            <button onclick="enableScanners()">Enable Barcode Scanners</button>
        </body>              
    </html>
{{< / highlight >}}

> *What does this means?*  
> **There may be multiple ways to achieve the same result in EB!**

### Example 1: Scale Webpage to a different size

One common request when users/partners needs to use and existing web application to a new device is to adapt the dimension to the available screen-size.

A very easy way to customize this in Enterprise Browser is to configure the Zoom parameters in `Config.xml`:

{{< highlight xml "linenos=table" >}}
    <Screen>
        <FullScreen value="1"/>
        <PageZoom value="1.0" />
        <EnableZoom value="1"/>
    </Screen>
{{< / highlight >}}

Looking at the [documentation for `PageZoom`](https://techdocs.zebra.com/enterprise-browser/1-8/guide/configreference/#pagezoom):

> ### PageZoom  
>
> Sets the zoom factor of the page.  
> Default zoom value is 1.0 (if unspecified). On Android, zero and negative values are not supported. On Windows, zoom value less than 1.0 reverts to 1.0 since lower values would not be readable. Page zoom settings will sometimes be reflected a few milliseconds after navigating from one page to another. A one-second delay should be anticipated. Not Supported when using Internet Explorer as the rendering engine.

Wait, what do you mean that *"On Windows, zoom value less than 1.0 reverts to 1.0."*?

Well, it means that if you're trying to use a web application built for a larger desktop screen, you cannot scale it down with this configuration!  
Are we out of luck?  
of course not! we can digg up the `Zoom` metatag that allows to scale up and down a webpage.
To have this applied to all the pages, we can use the `DefaultMetaTags` portion in the `Config.xml`: 

{{< highlight xml "linenos=table" >}}
    <DefaultMetaTags>
        <MetaTag VALUE=“zoom~page:0.5;text:1” />
    </DefaultMetaTags>
{{< / highlight >}}

So, you can not only zoom out the page, but you can keep the text at the default zoom.

## Customize screen size

This is a common topic, that we just saw has a very simple answer using the `PageZoom` option in EB's `Config.xml` and, because usually you want to Zoom in on an existing Web Application, that's cover most of the cases.

We saw another option with the `Zoom` Tag that usually needs to be included in every page of the web application but, we can cover this with the `DefaultMetaTags` option in EB's `Config.xml`.

### But wait, there's more!

Enterprise Browser v1.3 introduced support for DOM Injection that allows to add elements to the DOM. We can use this functionality to "inject" on every page the `Zoom` tag (or do more fancy stuffs that we're describing later).  
Where this DOM Injection functionality can be very helpful is that it allows us to specify a subset of pages where we want to inject the tag.  Think about wanting to zoom in only on the login page.

In this case we need to add a reference to our "tags file" that describe the elements that we want to add to the pages. This needs to reside on the device and is going to be referenced in EB's `Config.xml` by the [`CustomDomElements` tag](https://techdocs.zebra.com/enterprise-browser/1-8/guide/configreference/#dominjection):

{{< highlight xml "linenos=table" >}}
    <CustomDOMElements value="file://%INSTALLDIR%\di_tags.txt"/>
{{< / highlight >}}

We can then specify that we want to have the `Zoom` tag injected on all the pages:

{{< highlight xml "linenos=table" >}}
    <!-- Zoom in all pages-->
    <META HTTP-Equiv="zoom" Content="page:0.5" pages='*'/> 
{{< / highlight >}}

or just on a subset (in theory):

{{< highlight xml "linenos=table" >}}
    <!-- Zoom in only the login page-->
    <META HTTP-Equiv="zoom" Content="page:1.0" pages='*'/> 
    <META HTTP-Equiv="zoom" Content="page:0.5" pages='/login.html'/> 
{{< / highlight >}}

But here's where we can start to see the limits of this technology: the tag is injected after that the DOM is rendered and this generate some strange behavior (you see that the zoom is taking effect only you navigate to the next page).

### First thing first... what's DOM Injection?

From [EB's documentation](https://techdocs.zebra.com/enterprise-browser/1-8/guide/DOMinjection/)

> ### Overview
>
> Apps made with Enterprise Browser 1.3 and higher are able to perform DOM Injection, the ability insert CSS, JavaScript and/or meta tags into the DOM without modifying the underlying application. This permits features, capabilities and even the look of one or more server-based Enterprise Browser app pages to be modified at runtime using DOM elements stored in a text file on the device.

### A better sample of DOM Injection

A better sample for DOM Injection could be the necessity to select a screen orientation for an existing Web Application. This can be useful when you want to use a device in a particular orientation (landscape, as an example, for a wearable device) and you discover that the available options in `Config.xml` only allows you to disable the rotation. So, whatever is the selected orientation when you launch EB, it became the orientation that you're going to use:

{{< highlight xml "linenos=table" >}}
    <ScreenOrientation>
        <AutoRotate value="0" />
    </ScreenOrientation>
{{< / highlight >}}
 
A better solution can be enabled using DOM Injection and some easy JavaScript.

First of all, we can inject EB's JavaScript libraries in our `Config.xml` this allows us to have the libraries available:

{{< highlight xml "linenos=table" >}}
    <InjectEBLibraries>
	    <JSLibraries value="1"/>
    </InjectEBLibraries>
{{< / highlight >}}

Now we can inject a JavaScript file in our application as we have seen before, but this time the tag file needs to reference a script:

{{< highlight javascript "linenos=table" >}}
    <!-- Landscape all pages-->
    <script type="text/javascript" src="/landscape.js" pages="*" />
{{< / highlight >}}

And the `landscape.js` file itself:

{{< highlight javascript "linenos=table" >}}
    EB.ScreenOrientation.rightHanded();
    console.log('Landscape Configured');
{{< / highlight >}}

This guarantee that the web application is always using the correct screen orientation.

__Note:__ It may seems silly to include a `console.log` in such a simple JavaScript code, but it is useful for two reasons:

  1. It allows to see on the DevChrome Tools' console that the code has been injected
  2. It provides an easy way to retrieve the code (Chrome devTools put a link to the console statement close to the logged text)

### A final sample: adding barcode scanning capabilities to an application

As a final sample for DOM Injection I'm going to present one of the main use case for DOM Injection: adding barcode scanning capabilities to an existing application.  
We're going to focus on Android as the target operative system, and we know that, on this operative system, Zebra Technologies' devices can use DataWedge to scan barcodes. But with EB's DOM Injection we can achieve a much better integration!

Again, we start with some plumbing in `config.xml` enabling EB's JavaScript libraries injection to have the libraries available:

{{< highlight xml "linenos=table" >}}
    <InjectEBLibraries>
        <JSLibraries value="1"/>
    </InjectEBLibraries>
{{< / highlight >}}

Next, we need to add a reference to the tag file:

{{< highlight xml "linenos=table" >}}
    <CustomDOMElements value="file://%INSTALLDIR%\di_tags.txt"/>
{{< / highlight >}}

Now we can inject a JavaScript file in our application as we have seen before, but this time the tag file needs to reference a script:

{{< highlight javascript "linenos=table" >}}
    <!-- Landscape all pages-->
    <script type="text/javascript" src="/scanning.js" pages="*" />
{{< / highlight >}}

And the `scanning.js` file itself:

{{< highlight javascript "linenos=table" >}}
    function scanReceived(params){
        var barcodeStr = params['data'];
        
        //put the barcode into the field in focus
        document.activeElement.value = barcodeStr;
        
        //now we are going to place the data into the input field with id barcode1
        document.getElementById("barcode1").value = barcodeStr;
    }

    function enableScanners(){
        EB.Barcode.enable({}, scanReceived);
        // Empty property hash, '{}' loads default values for the scanner.
    }

    function unloadEvent(){
        EB.Barcode.disable();
        // Disable Barcode on unload of page to free it up for other operations.
    }

    enableScanners();
    console.log("file injected");
{{< / highlight >}}

This code is going to enable the barcode scanner with the default properties, then inserting the barcode data into the current element (the focused element).  
As an alternative example, if you need to read Interleaved 2of5 barcodes, you can enable the barcode API with this parameters:

{{< highlight javascript "linenos=table" >}}
    EB.Barcode.enable({i2of5:true, i2of5maxLength:30, i2of5minLength:4}, scanReceived);
{{< / highlight >}}

For a full list of the available properties take a look at [EB's Barcode API documentation](https://techdocs.zebra.com/enterprise-browser/1-8/api/barcode/).

## Adding a custom on-screen keyboard to an existing application

The official name for custom keyboards in Enterprise Browser is [ButtonBar](http://techdocs.zebra.com/enterprise-browser/1-8/guide/customize/). Let's take a look at what the documentation says about them:

> ### Overview
>
> Enterprise Browser 1.7 (and higher) for Android includes ButtonBar APIs, which can deliver custom features and functions through on-screen buttons or keys. Functions can include simple actions such as launching an app or activity, sending an intent or virtually any action that can be executed using JavaScript.

#### How it Works

The settings, parameters, actions and attributes of the desired on-screen button(s) are stored in an XML container called `Button.xml`. If any of those buttons are to execute JavaScript, the JavaScript code is contained in a second file called `CustomScript.xml`. Both files are stored on the device in the same folder of the `Config.xml` file.

ButtonBars can be shown and hidden programmatically as required by an app's pages through methods implemented in one of 50 ButtonBar APIs currently supported.

#### Let's see an example

Starting from the `Button.xml` this can be a quite complex file, where we've all the description of the keyboard layout. It's still a manual process at this moment, but we are planning to have a tool to help in the process. Still just a plan!

{{< highlight xml "linenos=table" >}}
    <?xml version = "1.0"?>
    <Buttonbargroup>
        
        <ButtonBar1>
            <barOrientation value="Horizontal"/>
            <barColor value="#003370" />
            <barColorPressed value="#003370" />
            <barTransparency value="0" />
            <barFontSize value="20" />
            <barGap value="0" />
            <barLeft value="0" />
            <barTop value="596" />
            <barWidth value="144" />
            <barHeight value="144" />
            <Buttons>
                <Button1>
                <buttonAction value ="runscript-togglescript1"/>
                <buttonImage value ="/storage/emulated/0/Android/data/com.symbol.enterprisebrowser/keyboard_up.png"/>
                </Button1>
            </Buttons>
        </ButtonBar1>
        
        <ButtonBar2>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="20" />
            <barGap value="2" />
            <barLeft value="560" />
            <barTop value="200" />
            <barWidth value="640" />
            <barHeight value="100" />
            <Buttons>
                <Button1>
                    <buttonText value ="7"/>
                    <buttonAction value ="key-14"/>
                </Button1>
                <Button2>
                    <buttonText value ="8"/>
                    <buttonAction value ="key-15"/>
                </Button2>
                <Button3>
                    <buttonText value ="9"/>
                    <buttonAction value ="key-16"/>
                </Button3>
            </Buttons>
        </ButtonBar2> 
        
        <ButtonBar3>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="20" />
            <barGap value="2" />
            <barLeft value="560" />
            <barTop value="302" />
            <barWidth value="640" />
            <barHeight value="100" />
            <Buttons>
                <Button1>
                    <buttonText value ="4"/>
                    <buttonAction value ="key-11"/>
                </Button1>
                <Button2>
                    <buttonText value ="5"/>
                    <buttonAction value ="key-12"/>
                </Button2>
                <Button3>
                    <buttonText value ="6"/>
                    <buttonAction value ="key-13"/>
                </Button3>
            </Buttons>
        </ButtonBar3> 
        
        <ButtonBar4>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="20" />
            <barGap value="2" />
            <barLeft value="560" />
            <barTop value="404" />
            <barWidth value="640" />
            <barHeight value="100" />
            <Buttons>
                <Button1>
                    <buttonText value ="1"/>
                    <buttonAction value ="key-8"/>
                </Button1>
                <Button2>
                    <buttonText value ="2"/>
                    <buttonAction value ="key-9"/>
                </Button2>
                <Button3>
                    <buttonText value ="3"/>
                    <buttonAction value ="key-10"/>
                </Button3>
            </Buttons>
        </ButtonBar4>
        
        <ButtonBar5>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="20" />
            <barGap value="0" />
            <barLeft value="560" />
            <barTop value="506" />
            <barWidth value="212" />
            <barHeight value="100" />
            <Buttons>
                <Button1>
                    <buttonText value ="0"/>
                    <buttonAction value ="key-7"/>
                </Button1>	
            </Buttons>
        </ButtonBar5>
        
        <ButtonBar6>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="60" />
            <barGap value="0" />
            <barLeft value="774" />
            <barTop value="506" />
            <barWidth value="212" />
            <barHeight value="208" />
            <Buttons>
                <Button5>
                    <buttonText value="↵"/>
                    <buttonAction value ="key-160"/>
                    <!-- <buttonImage value ="/storage/emulated/0/Android/data/com.symbol.enterprisebrowser/enter.png"/> -->
                </Button5>
            </Buttons>
        </ButtonBar6>
        
        <ButtonBar7>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="30" />
            <barGap value="0" />
            <barLeft value="988" />
            <barTop value="506" />
            <barWidth value="212" />
            <barHeight value="100" />
            <Buttons>
                <Button5>
                    <buttonText value="⬅︎"/>
                    <buttonAction value ="key-67"/>
                    <!-- <buttonImage value ="/storage/emulated/0/Android/data/com.symbol.enterprisebrowser/delete.png"/> -->
                </Button5>
            </Buttons>
        </ButtonBar7> 
        
        <ButtonBar8>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="20" />
            <barGap value="0" />
            <barLeft value="560" />
            <barTop value="608" />
            <barWidth value="212" />
            <barHeight value="108" />
            <Buttons>
                <Button1>
                    <buttonText value ="F3"/>
                    <buttonAction value ="key-133"/>
                </Button1>
            </Buttons>
        </ButtonBar8> 
        
        <ButtonBar9>
            <barOrientation value="Horizontal"/>
            <barColor value="#0033cc" />
            <barColorPressed value="#0040ff" />
            <barTransparency value="70" />
            <barFontSize value="20" />
            <barGap value="0" />
            <barLeft value="988" />
            <barTop value="608" />
            <barWidth value="212" />
            <barHeight value="108" />
            <Buttons>
                <Button1>
                    <buttonText value ="F4"/>
                    <buttonAction value ="key-134"/>
                </Button1>
            </Buttons>
        </ButtonBar9>

        <ButtonBar10>
            <barOrientation value="Horizontal"/>
            <barColor value="#003370" />
            <barColorPressed value="#003370" />
            <barTransparency value="0" />
            <barFontSize value="20" />
            <barGap value="0" />
            <barLeft value="0" />
            <barTop value="596" />
            <barWidth value="144" />
            <barHeight value="144" />
            <Buttons>
                <Button1>
                <buttonAction value ="runscript-togglescript2"/>
                <buttonImage value ="/storage/emulated/0/Android/data/com.symbol.enterprisebrowser/keyboard_down.png"/>
                </Button1>
            </Buttons>
        </ButtonBar10>
        
    </Buttonbargroup>
{{< / highlight >}}

This is a sample that I've implemented for Enterprise Browser v1.7. The current version 1.8 introduces some new features that allows to use relative Coordinates for ButtonBar positioning

{{< highlight xml "linenos=table" >}}
    <barLeft value="0.25*devicewidth"/>    
    <barTop value="0.25*deviceheight"/>
    <barLeft value="0.5*devicewidth"/>
    <barheight value="deviceheight-100"/>
    <barwidth value="devicewidth/2"/>
{{< / highlight >}}

This allows to build button bars that can be used on multiple devices (maintaining a similar screen geometry), without having to create and maintain a different keyboard for every device.

### Debugging Techniques - aka: Real Life Survival Guide

#### `Config.xml`

One of the major cause of issues with Enterprise Browser is using an old `Config.xml` (from a previous EB's version); so, my main troubleshooting technique is to always start with a fresh `Config.xml` using this steps:

  1. Install a new version of EB on a clean device (EB creates a new `Config.xml` only if there's none in `/sdcard/Android/data/com.symbol.enterprisebrowser`)
  2. Launch EB
  3. Close EB
  4. Copy the newly created configuration from `/sdcard/Android/data/com.symbol.enterprisebrowser/Config.xml`

EB v1.8 now includes some information about the version that created the file and this information is displayed in EB's Log:

{{< highlight xml "linenos=table" >}}
    <?xml version = "1.0"?>
    <!--
    EnterpriseBrowser_v1.8.0.0 Configuration file
    -->
    <Configuration>
        <EB_VERSION value="1.8.0.0"/>

        :   :   :   :   :   :   :   :

    </Configuration>
{{< / highlight >}}

##### Logging and WebPage Capture

Customer applications are mostly on premises solution with no access from outside networks. EB introduced ways to enable collecting logs and debug and web page captures.

EB's includes some powerful logging capabilities that can be enabled and configured inside EB's `Config.xml`, [take a look at the documentation for a full list of the options](https://techdocs.zebra.com/enterprise-browser/1-8/guide/logging/):

{{< highlight xml "linenos=table" >}}
    <Logger>
        <LogProtocol   value="FILE"/>
        <LogPort       value="80"/>
        <LogURI        value="file://%INSTALLDIR%/Log.txt"/>
        <LogError      value="1"/>
        <LogWarning    value="1"/>
        <LogInfo       value="1"/>
        <LogTrace      value="0"/>
        <LogUser       value="0"/>
        <LogMemory     value="0"/>
        <LogMemPeriod  value="5000"/>
        <LogMaxSize    value="1000"/>
    </Logger>
{{< / highlight >}}

Another powerful option for debugging remote application is to enable [WebPage Capture](https://techdocs.zebra.com/enterprise-browser/1-8/guide/capture/). In this case EB's is going to save a copy of the visited web pages and a screenshot on the divide for later analysis:

{{< highlight xml "linenos=table" >}}
    <Diagnostic>
        <WebPageCapture value="1"/>
    </Diagnostic> 
{{< / highlight >}}

With this option enabled you can find screenshots and webpages source under the Diagnostic folder:

![WebPAge Capture](/images/20180321_eb_modernization/webpage_capture.png "WebPage Capture")

##### On Device debugging

Using Chrome Dev Tools, it is possible to debug application running on mobile device. For Android, the only requirement is to run Android v4.4.x or newer and to enable the debug capabilities in EB's `Config.xml`:

{{< highlight xml "linenos=table" >}}
    <DebugSetting>
        <DebugModeEnable value="1"/>
    </DebugSetting> 
{{< / highlight >}}

You can find more information on this feature and how to use it for legacy windows devices on [EB's documentation](https://techdocs.zebra.com/enterprise-browser/1-8/guide/debuggingjs/)

#### Chrome Dev Tools

[Chrome DevTools allows to debug remote devices connected through the ADB protocol](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/).

Once you've youre device connected to your computer with a working ADB connection, you can launch the remote debug tools from inside a Chrome webpage:

![Chrome Dev Tools - Inspect a page](/images/20180321_eb_modernization/ChromeDevTools_1.png "Chrome Dev Tools - Inspect a page")

Then, from the `More Tools` menu, you can select ~Remote Devices~:

![Chrome Dev Tools - More Tools](/images/20180321_eb_modernization/ChromeDevTools_2.png "Chrome Dev Tools - More Tools")

In the `Remote Devices` panel, you should be able to see your device (connected to the computer through `adb`) and select which webpage you want to inspect:

![Chrome Dev Tools - Inspect remote device](/images/20180321_eb_modernization/ChromeDevTools_3.png "Chrome Dev Tools - Inspect remote device")

Once you select the remote page that you want to inspect, ChromeDev Tools will show you a rendering of the remote display.  
*Note:* Chrome only shows the elements inside the DOM. Other elements like a Custom ButtonBar or an on screen Keyboard is not going to be shown.

![Chrome Dev Tools - Debug from device](/images/20180321_eb_modernization/ChromeDevTools_4.png "Chrome Dev Tools - Debug from device")

That's all for now.  
Zebra's Enterprise Browser team has put a lot of resources on [Launchpad](https://developer.zebra.com/community/welcome) and on [Techdocs](https://techdocs.zebra.com/enterprise-browser/). If you have any question, you can use [EB's discussion forum on Launchpad](https://developer.zebra.com/community/home/discussions/enterprise-browser).
