# Jukko SDK documentation

Welcome to the Jukko developer SDK documentation. Follow our step-by-step instructions to
integrate Jukko and start monetizing while converting your app into a catalyst for social
impact today!

If you have any questions during the integration process, you can reach us at [devs@jukko.com](mailto:devs@jukko.com)
and we'll get back to you ASAP. Thanks for joining our movement to create a better world and
a better way of doing business.

1. [Integration](#integration)
2. [Usage](#usage)
    * [Initialization](#usage)
    * [Showing an ad](#showing-an-ad)
    * [Frequency capping](#frequency-capping)
    * [Console logging](#console-logging)
3. [Android Specific information](#android-specific-information)
    * [Google Play Services](#google-play-services)
    * [Requirements](#requirements)
3. [iOS Specific information](#ios-specific-information)

### Integration
1. **Download:** [Jukko Unity SDK](https://dl.jukko.com/unity/JukkoUnitySDK.unitypackage).
2. Import `JukkoUnitySDK.unitypackage` file into your `Assets` directory in Unity (or open your project and double click the `JukkoUnitySDK.unitypackage` in file explorer). This should create 2 sub directories:
    * **JukkoSDK** - Jukko script files
    * **Plugins** - rest of Jukko (native integration)

3. Use the Jukko framework in your project. Take a look at the `JukkoUnitySDK/Jukko.cs` files to see available classes and interfaces and list of the supported methods. 

### Usage

##### Initialization
Initialization has to be done before Jukko SDK can be customized and launched. The method will
need an API key. The API key can be generated in the dashboard after registration.
You can register on [Jukko website](https://jukko.com).

```csharp
    JukkoSdk.Instance.Init ("YOUR_API_KEY");
```

In case You are building application for both Android and iOS, You should get separate keys for each platform.
Initialization will look like following:

```csharp
    #if UNITY_ANDROID
    JukkoSdk.Instance.Init ("ANDROID_API_KEY");
    #elif UNITY_IOS
    JukkoSdk.Instance.Init ("IOS_API_KEY");
    #endif
```


##### Showing an ad

You can show an ad by calling `ShowAd ()` method of Jukko SDK:

```csharp
        JukkoSdk.Instance.ShowAd ((JukkoSdk.AdClosedEvent e) => {
            //handle AdClosedEvent here
        });
```

When ad UI is closed, `AdClosed` delegeate will be called. It will contain thefollowing information:

1. `Reason`: reason why the ad was closed. Possible values are:
    * `ClosedByUser`: Ad view was closed by user interaction.
    * `Timeout`: API servers were unresponsive.
    * `NetworkConnectivity`: Network connectivity issue.
    * `FrequencyCapping`: `ShowAd ()` called before frequency capping timeout ended.
    * `Error`: Unspecified error. Look at the `Message` property for description.
2. `Message`: string containing extended description of reason.
3. `Events`: list of events that happened with ad UI. May be empty. Each event contains:
    * `timestamp` of the event (uses current timezone).
    * `adEvent` type of event. Possible values:
        * `Launch`: Ad UI opened.
        * `Close`: Ad UI closed.
        * `IntroShown`: NPO campaign intro was shown to user.
        * `ProgressShown`: NPO campaign progress was shown to user.
        * `OutroShown`: NPO campaign outro was shown to user.
        * `AdShown`: Ad was shown to user.
        * `AdUrlOpened`: user clicked on URL, that was opened in external browser.
        * `Error`: Unspecified error.


##### Frequency capping

Jukko SDK allows developer to set frequency capping for ads. It counts time since the last time when an ad was closed
and ignores `ShowAd ()` calls until frequency capping period ends. Frequency capping can be changed
using:

```csharp
    JukkoSdk.Instance.AdsFrequency = timeInSeconds;
```


##### Console logging

By default, Jukko SDK logs only important messages, like unrecoverable error reasons.

You can enable debug logging by calling:

```csharp
    JukkoSdk.Instance.DebugMode = true;
```

SDK will use native log messaging. Log messages will contain `Jukko SDK` tag.


### Android Specific information

##### Google Play Services

If user has Google Play services on their devices, SDK will automatically use user's
Advertising ID (GAID) and Limit Ad Tracking setting.

For more information about Google Advertising ID visit [this link](https://play.google.com/about/monetization-ads/ads/ad-id/).

##### Requirements

Jukko SDK supports devices starting with Android 4.0 (API level 14). However, due to some older WebView version restrictions, ad UI won't be shown for Android prior to 4.4 (API level < 19) and SDK will only log some messages in console specifying this.

### iOS Specific information