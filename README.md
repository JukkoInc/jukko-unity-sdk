># Jukko Unity Documentation

![Jukko](http://res.cloudinary.com/jukko-dev/image/upload/v1503341726/GitHub_image_dxz5fk.png "Jukko Banner")

Welcome to the Jukko developer SDK documentation. Follow our step-by-step instructions to
integrate Jukko and start monetizing while converting your app into a catalyst for social
impact today!

If you have any questions during the integration process, you can reach us at [devs@jukko.com](mailto:devs@jukko.com) and we'll get back to you ASAP. Thanks for joining our movement to create a better world and a better way of doing business.


### Integration
1. **Download:** [Jukko Unity SDK](https://s3.amazonaws.com/jukkosdk/unity/JukkoUnitySDKProduction-2.0.2.unitypackage).
2. Open your project in the Unity Editor. Open **Assets - Import Package - Custom Package... ** and select `JukkoUnitySDK.unitypackage` file:

    ![](images/asset-import-1.png)

3. Check that all files and directories are selected and click **Import**:
    
    ![](images/asset-import-2.png)
    
    This operation should create 2 subdirectories inside `Assets` folder:

    *  **JukkoSDK** - Jukko script files

    *  **Plugins** - rest of Jukko (native integration)  

4. Use the Jukko framework in your project. Take a look at the `Assets/JukkoSDK/Jukko.cs` files to see available classes and interfaces and a list of the supported methods. 

### Usage

##### Initialization
Initialization has to be done before Jukko SDK can be customized and launched. The method will
need an API key. The API key can be generated in the dashboard after registration.
You can register on the [Jukko dashboard](https://dashboard.staging.jukko.com).

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
        JukkoSdk.Instance.ShowAd ((AdClosedEvent e) => {
            //handle AdClosedEvent here
        });
```

When ad UI is closed, `AdClosed` delegate will be called. It will contain the following information:

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
and ignores `ShowAd ()` calls until frequency capping period ends. Frequency capping can be changed using:

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

Your application should also have compileSdkVersion set to 25 or higher. Jukko SDK supports devices starting with Android 4.0 (API level 14). However, due to some older WebView version restrictions, ad UI won't be shown for Android prior to API level 19 and you will only be exposed to some some for the console messages.

### iOS Specific information

##### Requirements
Jukko SDK support devices starting with iOS 10.0.

##### iOS Project configuration

1. In Unity go to **File > Build Settings** and choose iOS from the list. Click `Build`. This will generate an iOS project that you need to open with Xcode. More information about this can be found in the Unity documentation.

2. In Xcode go to Project **Settings > General > Linked Frameworks And Libraries** and remove any existing Jukko framework.

3. In the Xcode file explorer, expand the files on the left to **Frameworks > Plugins > iOS**. Drag the JukkoFramework.framework into **Project Settings > General > Embedded Binaries** section. This will add it to **Linked Frameworks And Libraries** section as well (this is what we want).

    ![](images/ios_drag_framework.png)

4. Under the Build Settings find `Always Embed Swift Standard Libraries` (Xcode 8) and set it to Yes.


    ![](images/ios_embed_libraries.png)

5. Ensure that project's Deployment Target version is equal or above 10.0.

6. Apple doesn't allow to publish applications with embedded frameworks containing binaries for simulator: i386, x86_64.
To strip all used frameworks from those binaries:

    1. Select `Build Phases`, add new `New Run Script Phase` and place it after `Embed Frameworks` phase.
    2. Add script text or executable shell script.

Script text:

```
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"

# This script loops through the frameworks embedded in the application and
# removes unused architectures.
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"

EXTRACTED_ARCHS=()

for ARCH in $ARCHS
do
echo "Extracting $ARCH from $FRAMEWORK_EXECUTABLE_NAME"
lipo -extract "$ARCH" "$FRAMEWORK_EXECUTABLE_PATH" -o "$FRAMEWORK_EXECUTABLE_PATH-$ARCH"
EXTRACTED_ARCHS+=("$FRAMEWORK_EXECUTABLE_PATH-$ARCH")
done

echo "Merging extracted architectures: ${ARCHS}"
lipo -o "$FRAMEWORK_EXECUTABLE_PATH-merged" -create "${EXTRACTED_ARCHS[@]}"
rm "${EXTRACTED_ARCHS[@]}"

echo "Replacing original executable with thinned version"
rm "$FRAMEWORK_EXECUTABLE_PATH"
mv "$FRAMEWORK_EXECUTABLE_PATH-merged" "$FRAMEWORK_EXECUTABLE_PATH"

done

```
![](http://res.cloudinary.com/jukko-staging/image/upload/v1503509992/Screen_Shot_2017-08-23_at_12.39.21_PM_qxf2hf.png) 
7. Build and run the project on your device.
