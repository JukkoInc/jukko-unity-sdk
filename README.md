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
    * [Ad showing](#ad-showing)
    * [Frequnecy capping](#frequnecy-capping)
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
Initialization has to be done before Jukko can be customized and launched. The method will
need the API key and context. The API key can be generated in the dashboard after registration.
You can register on [Jukko website](https://jukko.com).

```csharp
    JukkoSdk.Instance.Init ("YOUR_API_KEY");
```

##### Ad showing

You can show an ad by calling `ShowAd ()` method:

```csharp
        JukkoSdk.Instance.ShowAd ((JukkoSdk.AdClosedEvent e) => {
            //handle AdClosedEvent here
        });
```

When ads activity is finished, `AdClosed` delegeate will be called. It will following information:

1. `Reason`: reason why ad was closed. Possible variants are:
    * `ClosedByUser`: Ad view was closed by user interactions
    * `Timeout`: Api servers were unresponsive
    * `NetworkConnectivity`: Network connectivity problems
    * `FrequencyCapping`: `ShowAd ()` called before frequency capping timeout ended
    * `Error`: Unspecified error. Look at the `Message` property for description
2. `Message`: string containing extended description of reason
3. `Events`: list of events that happened with ad activity. May be empty. Each event contains:
    * `timestamp` of the event (uses current timezone)
    * `adEvent` type of event. Possible variants:
        * `Launch`: Ad activity opened
        * `Close`: Ad activity closed
        * `IntroShown`: NPO campaign intro was shown to user
        * `ProgressShown`: NPO campaign progress was shown to user
        * `OutroShown`: NPO campaign outro was shown to user
        * `AdShown`: Ad was shown to user
        * `AdUrlOpened`: user clicked on url, that opened in external browser
        * `Error`: Unspecified error.


##### Frequency capping

Sdk allows developer to set frequency capping for ads. It counts time since last ad was closed
and ignores `ShowAd ()` calls in frequency capping period. Frequency capping can be changed
using:

```csharp
    JukkoSdk.Instance.AdsFrequency = timeInSeconds;
```


##### Console logging

By default, Jukko SDK logs only important messages, like unrecoverable error reasons.

You can enable debug logging by calling

```csharp
    JukkoSdk.Instance.DebugMode = true;
```

SDK will use native log messaging. Log messages will contain `Jukko SDK` tag.


### Android Specific information

##### Google Play Services

If user have Google Play services on their devices, SDK will automatically use its
Advertising ID (GAID) and Limit Ad Tracking information.

For more information about Google Advertising ID visit [this link](https://play.google.com/about/monetization-ads/ads/ad-id/).

##### Requirements

Jukko SDK support devices starting with Android 4.0 (API level 14).

However, SDK won't do anything for Android prior to 4.4 (API level < 19) except log messages.

### iOS Specific information