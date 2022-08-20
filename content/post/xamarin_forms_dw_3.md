---
title: "(Xamarin.Forms + FreshMVVM + DataWedge) take 2"
summary: "We're going to review the code presented in the last post to add some more functionalities and make it a bit more modular."
date: 2018-07-11T09:00:00+02:00
type: "post"
draft: false
author: "Pietro F. Maggi"
tags: ["Android", "Zebra", "Xamarin", "Xamarin.Forms", "FreshMVVM", "DataWedge"]
---

*Note: [the accompanying code is available on GitHub](https://github.com/Zebra/Inventory/releases/tag/datawedge2).*

We saw in the [last post]({{ relref . "xamarin_forms_dw_2.md" }}) how to add barcode scanning functionalities to the "Inventory" sample application, using DataWedge.

Today we're going to review that code and refactor it "just a bit". The goal is to make it a bit more modular, adding some flexibility to how we manage the barcode. In particular we want to have these features in our app:

- Be able to enable/disable the barcode scanner (we don't want the barcode to be always active).
- Be able to configure the barcode scanner decoders and parameters in general.
- Get the data in the Xamarin Forms application using an event handler directly instead of using the Messaging Center.

## An interface to the scanner

The first step to modularize the application is to create an interface to abstract the barcode scanning functionality. In the portable library project create an `Interfaces` folder and create a new interface inside it with the name `IScanner`:



The code for this interface is:

    using System;
    namespace Inventory
    {
        /// <summary>
        /// This is the interface for the Scanner code.
        /// This interface defines how we use the Scanner, a matching class needs to be implemented
        /// on each platform as well.
        /// </summary>
        public interface IScanner
        {
            event EventHandler<StatusEventArgs> OnScanDataCollected;
            event EventHandler<string> OnStatusChanged;

            void Read();

            void Enable();

            void Disable();

            void OnScan(StatusEventArgs a_args);

            void OnStatus(String a_message);

            void SetConfig(IScannerConfig a_config);
        }
    }

We're going to create a second interface, with the name `IScannerConfig` to abstract the configuration of the barcode scanner. The code of this interface is going to be:

    using System;

    namespace Inventory
    {
        public interface IScannerConfig
        {
        }
    }

We're going to add few models to support this new integration of the barcode scanning functionalities.

## Some new models for the barcode data and events

We're still working at the portable project level, the Xamarin Forms application. Inside the `Models` folder, create a new `Barcode` class:

    using System;
    namespace Inventory
    {
        public class Barcode
        {
            private string data;
            public string Data
            {
                get { return data; }
                set { data = value; }
            }

            private string type;
            public string Type
            {
                get { return type; }
                set { type = value; }
            }

            private string info;
            public string Info
            {
                get { return $"{data} / {type}"; }
            }

            public Barcode() { }

            public Barcode(string a_data, string a_type)
            {
                data = a_data;
                type = a_type;
            }
        }
    }

Then we need another class, again in the `Models` folder, for the Events. Create a new class with the name `StatusEventArgs`:

    using System;
    namespace Inventory
    {
        /// <summary>
        /// Custom event args for use by the scanner
        /// </summary>
        public class StatusEventArgs : EventArgs
        {
            private string barcodeData;

            public StatusEventArgs(string dataIn, string barcodeTypeIn)
            {
                barcodeData = dataIn;
                barcodeType = barcodeTypeIn;
            }

            public string Data { get { return barcodeData; } }

            private string barcodeType;
            public string BarcodeType { get { return barcodeType; } }

        }
    }

The last model class we need to create is the one that bring some knowledge of Zebra's DataWedge functionality in the portable project. In reality, this doesn't stops the application to work on other device or to have the application built for iOS. Simply we want to control DataWedge from the Xamarin.Forms code and, if we want to be able to run the application on other Android devices or on iOS, we simply need to take in account that some of these features are not going to be available on those devices.

Inside the `Models` folder create a `ZebraScannerConfig` class:

    using System;

    namespace Inventory
    {
        public class ZebraScannerConfig : IScannerConfig
        {
            public TriggerType TriggerType { get; set; }

            public bool IsEAN8 { get; set; }
            public bool IsEAN13 { get; set; }
            public bool IsCode39 { get; set; }
            public bool IsCode128 { get; set; }
            public bool IsContinuous { get; set; }
            public bool IsUPCA { get; set; }
            public bool IsUPCE0 { get; set; }
            public bool IsUPCE1 { get; set; }
            public bool IsD2of5 { get; set; }
            public bool IsI2of5 { get; set; }
            public bool IsAztec { get; set; }
            public bool IsPDF417 { get; set; }
            public bool IsQRCode { get; set; }

            public ZebraScannerConfig()
            {
                IsEAN8 = true;
                IsEAN13 = true;
                IsCode39 = true;
                IsCode128 = true;
                IsUPCA = true;
                IsUPCE0 = true;
                IsUPCE1 = true;
                IsD2of5 = false;
                IsI2of5 = true;
                IsAztec = false;
                IsPDF417 = true;
                IsQRCode = true;

                IsContinuous = true;
                TriggerType = TriggerType.HARD;
            }
        }

        public enum TriggerType
        {
            HARD,
            SOFT
        }
    }

## Implementing the IScanner interface with DataWedge's Intent APIs

It's now the moment to implement the `IScanner` interface on top of DataWedge's Intent APIs. These changes are done to the Android project inside our solution, in the `Inventory.Droid` namespace.

Create a new class with the name `Scanner_Android`. This is going to contain the bulk of (all) the logic to control DataWedge:

    using System;
    using Android.App;
    using Android.Content;
    using Android.OS;

    namespace Inventory.Droid
    {
        public class Scanner_Android : IScanner
        {
            private Context _context = null;
            private bool _bRegistered = false;
            private DataWedgeReceiver _broadcastReceiver = null;
            private static string ACTION_DATAWEDGE_FROM_6_2 = "com.symbol.datawedge.api.ACTION";
            private static string EXTRA_CREATE_PROFILE = "com.symbol.datawedge.api.CREATE_PROFILE";
            private static string EXTRA_SET_CONFIG = "com.symbol.datawedge.api.SET_CONFIG";
            private static string EXTRA_PROFILE_NAME = "Inventory DEMO";

            public Scanner_Android()
            {
                _context = Application.Context;

                _broadcastReceiver = new DataWedgeReceiver();

                _broadcastReceiver.scanDataReceived += (s, scanData) =>
                {
                    OnScanDataCollected?.Invoke(this, scanData);
                };

                CreateProfile();
            }

            public event EventHandler<StatusEventArgs> OnScanDataCollected;
            public event EventHandler<string> OnStatusChanged;


            public void Disable()
            {
                if ((null != _broadcastReceiver) && (null != _context) && _bRegistered)
                {
                    // Unregister the broadcast receiver
                    _context.UnregisterReceiver(_broadcastReceiver);
                    _bRegistered = false;
                }

                DisableProfile();
            }

            public void Enable()
            {
                _context = Application.Context;

                if ((null != _broadcastReceiver) && (null != _context))
                {
                    // Register the broadcast receiver
                    IntentFilter filter = new IntentFilter(DataWedgeReceiver.IntentAction);
                    filter.AddCategory(DataWedgeReceiver.IntentCategory);
                    _context.RegisterReceiver(_broadcastReceiver, filter);
                    _bRegistered = true;
                }

                EnableProfile();
            }

            public void Read()
            {
                // We can use this to activate a Soft triggered barcode scanning decoding
                throw new NotImplementedException();
            }

            public void SetConfig(IScannerConfig a_config)
            {

                ZebraScannerConfig config = (ZebraScannerConfig)a_config;

                Bundle profileConfig = new Bundle();
                profileConfig.PutString("PROFILE_NAME", EXTRA_PROFILE_NAME);
                profileConfig.PutString("PROFILE_ENABLED", _bRegistered ? "true" : "false"); //  Seems these are all strings
                profileConfig.PutString("CONFIG_MODE", "UPDATE");
                Bundle barcodeConfig = new Bundle();
                barcodeConfig.PutString("PLUGIN_NAME", "BARCODE");
                barcodeConfig.PutString("RESET_CONFIG", "false"); //  This is the default but never hurts to specify
                Bundle barcodeProps = new Bundle();
                barcodeProps.PutString("scanner_input_enabled", "true");
                barcodeProps.PutString("scanner_selection", "auto"); //  Could also specify a number here, the id returned from ENUMERATE_SCANNERS.
                                                                    //  Do NOT use "Auto" here (with a capital 'A'), it must be lower case.
                barcodeProps.PutString("decoder_ean8", config.IsEAN8 ? "true" : "false");
                barcodeProps.PutString("decoder_ean13", config.IsEAN13 ? "true" : "false");
                barcodeProps.PutString("decoder_code39", config.IsCode39 ? "true" : "false");
                barcodeProps.PutString("decoder_code128", config.IsCode128 ? "true" : "false");
                barcodeProps.PutString("decoder_upca", config.IsUPCA ? "true" : "false");
                barcodeProps.PutString("decoder_upce0", config.IsUPCE0 ? "true" : "false");
                barcodeProps.PutString("decoder_upce1", config.IsUPCE1 ? "true" : "false");
                barcodeProps.PutString("decoder_d2of5", config.IsD2of5 ? "true" : "false");
                barcodeProps.PutString("decoder_i2of5", config.IsI2of5 ? "true" : "false");
                barcodeProps.PutString("decoder_aztec", config.IsAztec ? "true" : "false");
                barcodeProps.PutString("decoder_pdf417", config.IsPDF417 ? "true" : "false");
                barcodeProps.PutString("decoder_qrcode", config.IsQRCode ? "true" : "false");

                barcodeConfig.PutBundle("PARAM_LIST", barcodeProps);
                profileConfig.PutBundle("PLUGIN_CONFIG", barcodeConfig);
                Bundle appConfig = new Bundle();
                appConfig.PutString("PACKAGE_NAME", Android.App.Application.Context.PackageName);      //  Associate the profile with this app
                appConfig.PutStringArray("ACTIVITY_LIST", new String[] { "*" });
                profileConfig.PutParcelableArray("APP_LIST", new Bundle[] { appConfig });
                SendDataWedgeIntentWithExtra(ACTION_DATAWEDGE_FROM_6_2, EXTRA_SET_CONFIG, profileConfig);

            }

            private void EnableProfile()
            {
                //  Now configure that created profile to apply to our application
                Bundle profileConfig = new Bundle();
                profileConfig.PutString("PROFILE_NAME", EXTRA_PROFILE_NAME);
                profileConfig.PutString("PROFILE_ENABLED", "true"); //  Seems these are all strings
                profileConfig.PutString("CONFIG_MODE", "UPDATE");
                SendDataWedgeIntentWithExtra(ACTION_DATAWEDGE_FROM_6_2, EXTRA_SET_CONFIG, profileConfig);
            }

            private void DisableProfile()
            {
                //  Now configure that created profile to apply to our application
                Bundle profileConfig = new Bundle();
                profileConfig.PutString("PROFILE_NAME", EXTRA_PROFILE_NAME);
                profileConfig.PutString("PROFILE_ENABLED", "false"); //  Seems these are all strings
                profileConfig.PutString("CONFIG_MODE", "UPDATE");
                SendDataWedgeIntentWithExtra(ACTION_DATAWEDGE_FROM_6_2, EXTRA_SET_CONFIG, profileConfig);
            }

            private void CreateProfile()
            {
                String profileName = EXTRA_PROFILE_NAME;
                SendDataWedgeIntentWithExtra(ACTION_DATAWEDGE_FROM_6_2, EXTRA_CREATE_PROFILE, profileName);

                //  Now configure that created profile to apply to our application
                Bundle profileConfig = new Bundle();
                profileConfig.PutString("PROFILE_NAME", EXTRA_PROFILE_NAME);
                profileConfig.PutString("PROFILE_ENABLED", "true"); //  Seems these are all strings
                profileConfig.PutString("CONFIG_MODE", "UPDATE");
                Bundle barcodeConfig = new Bundle();
                barcodeConfig.PutString("PLUGIN_NAME", "BARCODE");
                barcodeConfig.PutString("RESET_CONFIG", "true"); //  This is the default but never hurts to specify
                Bundle barcodeProps = new Bundle();
                barcodeConfig.PutBundle("PARAM_LIST", barcodeProps);
                profileConfig.PutBundle("PLUGIN_CONFIG", barcodeConfig);
                Bundle appConfig = new Bundle();
                appConfig.PutString("PACKAGE_NAME", Android.App.Application.Context.PackageName);      //  Associate the profile with this app
                appConfig.PutStringArray("ACTIVITY_LIST", new String[] { "*" });
                profileConfig.PutParcelableArray("APP_LIST", new Bundle[] { appConfig });
                SendDataWedgeIntentWithExtra(ACTION_DATAWEDGE_FROM_6_2, EXTRA_SET_CONFIG, profileConfig);
                //  You can only configure one plugin at a time, we have done the barcode input, now do the intent output
                profileConfig.Remove("PLUGIN_CONFIG");
                Bundle intentConfig = new Bundle();
                intentConfig.PutString("PLUGIN_NAME", "INTENT");
                intentConfig.PutString("RESET_CONFIG", "true");
                Bundle intentProps = new Bundle();
                intentProps.PutString("intent_output_enabled", "true");
                intentProps.PutString("intent_action", DataWedgeReceiver.IntentAction);
                intentProps.PutString("intent_delivery", "2");
                intentConfig.PutBundle("PARAM_LIST", intentProps);
                profileConfig.PutBundle("PLUGIN_CONFIG", intentConfig);
                SendDataWedgeIntentWithExtra(ACTION_DATAWEDGE_FROM_6_2, EXTRA_SET_CONFIG, profileConfig);
            }

            private void SendDataWedgeIntentWithExtra(String action, String extraKey, Bundle extras)
            {
                Intent dwIntent = new Intent();
                dwIntent.SetAction(action);
                dwIntent.PutExtra(extraKey, extras);
                _context.SendBroadcast(dwIntent);
            }

            private void SendDataWedgeIntentWithExtra(String action, String extraKey, String extraValue)
            {
                Intent dwIntent = new Intent();
                dwIntent.SetAction(action);
                dwIntent.PutExtra(extraKey, extraValue);
                _context.SendBroadcast(dwIntent);
            }
        }
    }

Part of the code that we've here was inside the Main Activity class before. I never liked the idea to have logic talking to DataWedge inside that class!

Now `Main_Activity` is cleaner:

    using System;
    using Android.App;
    using Android.Content.PM;
    using Android.OS;
    using FreshMvvm;

    namespace Inventory.Droid
    {
        [Activity(Label = "Inventory", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            private IScanner _scanner = null;

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);

                global::Xamarin.Forms.Forms.Init(this, bundle);

                var repository = new Repository(FileAccessHelper.GetLocalFilePath("items.db3"));
                FreshIOC.Container.Register(repository);

                _scanner = new Scanner_Android();
                FreshIOC.Container.Register(_scanner);

                LoadApplication(new App());
            }

        }
    }

As you can see we're now creating an instance of the `Scanner_Android` class and register it with FreshMVVM IOC. An alternative can be to use [Xamarin.Forms dependency service](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/app-fundamentals/dependency-service/introduction), but I liked the idea to use this IOC.
Swapping to the dependency service should be trivial (and is left as an exercise to the reader...).

Another required change, to the previous version of the application, is in the Broadcast receiver. We need to update it to send the barcode data using the `Barcode` class, instead than sending a string:

    using System;
    using Android.Content;

    namespace Inventory.Droid
    {
        [BroadcastReceiver]
        public class DataWedgeReceiver : BroadcastReceiver
        {
            // This intent string contains the source of the data as a string
            private static string SOURCE_TAG = "com.motorolasolutions.emdk.datawedge.source";
            // This intent string contains the barcode symbology as a string
            private static string LABEL_TYPE_TAG = "com.motorolasolutions.emdk.datawedge.label_type";
            // This intent string contains the captured data as a string
            // (in the case of MSR this data string contains a concatenation of the track data)
            private static string DATA_STRING_TAG = "com.motorolasolutions.emdk.datawedge.data_string";
            // Intent Action for our operation
            public static string IntentAction = "barcodescanner.RECVR";
            public static string IntentCategory = "android.intent.category.DEFAULT";

            public event EventHandler<StatusEventArgs> scanDataReceived;

            public override void OnReceive(Context context, Intent i)
            {
                // check the intent action is for us
                if (i.Action.Equals(IntentAction))
                {
                    // define a string that will hold our output
                    String Out = "";
                    String sLabelType = "";
                    // get the source of the data
                    String source = i.GetStringExtra(SOURCE_TAG);
                    // save it to use later
                    if (source == null)
                        source = "scanner";
                    // get the data from the intent
                    String data = i.GetStringExtra(DATA_STRING_TAG);
                    // let's define a variable for the data length
                    int data_len = 0;
                    // and set it to the length of the data
                    if (data != null)
                        data_len = data.Length;
                    // check if the data has come from the barcode scanner
                    if (source.Equals("scanner"))
                    {
                        // check if there is anything in the data
                        if (data != null && data.Length > 0)
                        {
                            // we have some data, so let's get it's symbology
                            sLabelType = i.GetStringExtra(LABEL_TYPE_TAG);
                            // check if the string is empty
                            if (sLabelType != null && sLabelType.Length > 0)
                            {
                                // format of the label type string is LABEL-TYPE-SYMBOLOGY
                                // so let's skip the LABEL-TYPE- portion to get just the symbology
                                sLabelType = sLabelType.Substring(11);
                            }
                            else
                            {
                                // the string was empty so let's set it to "Unknown"
                                sLabelType = "Unknown";
                            }

                            // let's construct the beginning of our output string
                            Out = data.ToString() + "\r\n";
                        }
                    }

                    if (scanDataReceived != null)
                    {
                        scanDataReceived(this, new StatusEventArgs(Out, sLabelType));
                    }
                }
            }
        }
    }

We've now done all the plumbing necessary to handle the barcode scanner in our application. Let's move back at the Xamarin.Forms level to see how we can now control the barcode scanner from there.

## Handle the barcode scanner in the Xamarin.Forms application

What we want to implement is to have the barcode scanner enabled only in the `ItemListPage` and disabled when we're in the `ItemPage`. In the previous version of the application ([presented in the previous post]({{ relref . "xamarin_forms_dw_2.md" }})), the barcode scanner was always enabled and there was some strange behavior sometimes.

We're going to modify the `ItemListPageModel` class implementing the two methods `ViewIsAppearing` and `ViewIsDisappearing` to enable and disable the barcode scanner:

    protected override void ViewIsAppearing(object sender, EventArgs e)
    {
        base.ViewIsAppearing(sender, e);
        var scanner = FreshIOC.Container.Resolve<IScanner>();

        scanner.Enable();
        scanner.OnScanDataCollected += ScannedDataCollected;
        scanner.OnStatusChanged += ScannedStatusChanged;

        var config = new ZebraScannerConfig();
        config.IsUPCE0 = false;
        config.IsUPCE1 = false;

        scanner.SetConfig(config);
    } 

    protected override void ViewIsDisappearing(object sender, EventArgs e)
    {
        var scanner = FreshIOC.Container.Resolve<IScanner>();

        if (null != scanner)
        {
            scanner.Disable();
            scanner.OnScanDataCollected -= ScannedDataCollected;
            scanner.OnStatusChanged -= ScannedStatusChanged;
        }
        base.ViewIsDisappearing(sender, e);
    }

We can see that here, we're not only enabling/disabling the barcode scanner so that is only active when this view is in the foreground. We're configuring the enabled decoders of the barcode and we're registering the event handler to call when we receive the data. Most of what we need to control the barcode scanner is here.

As we wrote before, we're using FreshMVVM's IOC to retrieve a reference to the active `IScanner` instance. Given that there's no implementation for iOS done at this moment, this application now works only on Android.

The complete `ItemListPageModel` class became:

    using FreshMvvm;
    using System;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.Linq;
    using System.Threading.Tasks;
    using System.Windows.Input;
    using Xamarin.Forms;

    namespace Inventory
    {
        public class ItemListPageModel : FreshBasePageModel
        {
            private Repository _repository = FreshIOC.Container.Resolve<Repository>();
            private Item _selectedItem = null;

            /// <summary>
            /// Collection used for binding to the Page's item list view.
            /// </summary>
            public ObservableCollection<Item> Items { get; private set; }

            /// <summary>
            /// Used to bind with the list view's SelectedItem property.
            /// Calls the EditItemCommand to start the editing.
            /// </summary>
            public Item SelectedItem
            {
                get { return _selectedItem; }
                set
                {
                    _selectedItem = value;
                    if (value != null) EditItemCommand.Execute(value);
                }
            }

            public ItemListPageModel()
            {
                Items = new ObservableCollection<Item>();
            }

            /// <summary>
            /// Called whenever the page is navigated to.
            /// Here we are ignoring the init data and just loading the items.
            /// </summary>
            public override void Init(object initData)
            {
                LoadItems();
                if (Items.Count() < 1)
                {
                    CreateSampleData();
                }

            }

            protected override void ViewIsAppearing(object sender, EventArgs e)
            {
                base.ViewIsAppearing(sender, e);
                var scanner = FreshIOC.Container.Resolve<IScanner>();

                scanner.Enable();
                scanner.OnScanDataCollected += ScannedDataCollected;
                scanner.OnStatusChanged += ScannedStatusChanged;

                var config = new ZebraScannerConfig();
                config.IsUPCE0 = false;
                config.IsUPCE1 = false;

                scanner.SetConfig(config);
            } 

            protected override void ViewIsDisappearing(object sender, EventArgs e)
            {
                var scanner = FreshIOC.Container.Resolve<IScanner>();

                if (null != scanner)
                {
                    scanner.Disable();
                    scanner.OnScanDataCollected -= ScannedDataCollected;
                    scanner.OnStatusChanged -= ScannedStatusChanged;
                }
                base.ViewIsDisappearing(sender, e);
            }

            /// <summary>
            /// Called whenever the page is navigated to, but from a pop action.
            /// Here we are just updating the item list with most recent data.
            /// </summary>
            /// <param name="returnedData"></param>
            public override void ReverseInit(object returnedData)
            {
                LoadItems();
                base.ReverseInit(returnedData);
            }

            /// <summary>
            /// Command associated with the add item action.
            /// Navigates to the ItemPageModel with no Init object.
            /// </summary>
            public ICommand AddItemCommand
            {
                get
                {
                    return new Command(async () => {
                        await CoreMethods.PushPageModel<ItemPageModel>();
                    });
                }
            }

            /// <summary>
            /// Command associated with the edit item action.
            /// Navigates to the ItemPageModel with the selected item as the Init object.
            /// </summary>
            public ICommand EditItemCommand
            {
                get
                {
                    return new Command(async (item) => {
                        await CoreMethods.PushPageModel<ItemPageModel>(item);
                    });
                }
            }

            /// <summary>
            /// Repopulate the collection with updated items data.
            /// Note: For simplicity, we wait for the async db call to complete,
            /// recommend making better use of the async potential.
            /// </summary>
            private void LoadItems()
            {
                Items.Clear();
                Task<List<Item>> getItemTask = _repository.GetAllItems();
                getItemTask.Wait();
                foreach (var item in getItemTask.Result)
                {
                    Items.Add(item);
                }
            }

            /// <summary>
            /// Uses the SQLite Async capability to insert sample data on multiple threads.
            /// </summary>
            private void CreateSampleData()
            {
                var item1 = new Item
                {
                    Name = "Milk",
                    Barcode = "8001234567890",
                    Quantity = 10
                };

                var item2 = new Item
                {
                    Name = "Soup",
                    Barcode = "8002345678901",
                    Quantity = 5
                };

                var item3 = new Item
                {
                    Name = "Water",
                    Barcode = "8003456789012",
                    Quantity = 20
                };

                var task1 = _repository.CreateItem(item1);
                var task2 = _repository.CreateItem(item2);
                var task3 = _repository.CreateItem(item3);

                // Don't proceed until all the async inserts are complete.
                var allTasks = Task.WhenAll(task1, task2, task3);
                allTasks.Wait();

                LoadItems();
            }

            private void ScannedDataCollected(object sender, StatusEventArgs a_status)
            {
                Barcode barcode = new Barcode();
                barcode.Data = a_status.Data;
                barcode.Type = a_status.BarcodeType;

                Item item;

                Task<List<Item>> getItemTask = _repository.GetItem(barcode.Data);
                getItemTask.Wait();
                if (getItemTask.Result.Count() < 1)
                {
                    item = new Item { Name = "", Barcode = barcode.Data };
                }
                else
                {
                    item = getItemTask.Result.First<Item>();
                }


                CoreMethods.PushPageModel<ItemPageModel>(item);

            }

            private void ScannedStatusChanged(object sender, string a_message)
            {
                string status = a_message;
            }
        }
    }

This is the last change made to the application.
The full project is now:

<span style="display:block;text-align:center">
![Final Inventory solution](/images/20180711_xamarin/final_solution.png "Final Inventory solution")
</span>

Thanks to `git`, you can see with a green dot the new files we created in this post, and with a yellow dot the files that we've modified.

We can now run it and enjoy having a much better control of the barcode scanner on a Zebra's Android device.

# **NOTE**

**I tried my best to present this topic in a clear way, I'm not a Xamarin expert, the code I'm presenting here works for me, on my computer with my TC56. Use this application as a reference, but don't assume that everything I wrote here is the definitive truth! Especially for mobile application developments; OS, tools and practices evolve very rapidly. What could be a good solution today, can be a terrible idea tomorrow.**
