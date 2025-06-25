---
title: Plugin integration
layout: home
parent: Flutter
nav_order: 2
---

## Prerequisites
* Flutter version at least `2.10.5`
### Android 
* `minSdkVersion` at least `24`
* `compileSdkVersion` at least `33`

Note: if you are getting errors about using wrong `minSdkVersion` or `compileVersion` even though you have set them to the ones above (or higher), you need to find `build.gradle` files that has `flutter.minSdkVersion` and `flutter.compileSdkVersion` and  manually change those values.

### iOS
* Minimum deployment target at least `12.0`

## pubspec.yaml
In your `pubspec.yaml` file’s dependencies add these lines to include Setupad's Prebid plugin for Flutter and run 'flutter pub get' command in terminal.
```yaml
dependencies:
  setupad_prebid_flutter: 1.0.1
```
In case the command fails, make sure you are logged into your Gitlab account using terminal.

## AndroidManifest.xml

The first step is to include your Google Ad Manager app ID into your app. For Android, locate your `AndroidManifest.xml` file, the path should look similar to this one: `Your project -> android -> app -> src -> main`.
After locating and opening this file, include the `<meta-data>` tag inside the `<application>` tag with your app ID, provided by Google Ad Manager.
```xml
<application>
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="ca-app-pub-################~##########"/>
    <!--...-->
</application>
```

## Info.plist
For iOS, to include yout Google Ad Manager ID into the project, you need to locate your 'Info.plist' file. The path to it should look similar to this one: `Your project -> ios -> Runner`. After locating and opening this file, include the `GADApplicationIdentifier` with your app ID< provided by Google Ad Manager. It is optional to include [SKAdNetworkItems] items to your `Info.plist` file.
```xml
<key>GADApplicationIdentifier</key>
<string>ca-app-pub-################~##########</string>
```

## SDK initialization
After including app ID into your project, next step is initializing the plugin. Prebid Mobile initialization is only needed to be done once and it is recommended to initialize it as early as possible in your project.
To initialize it, first include this import in your Dart file:
```dart
import 'package:setupad_prebid_flutter/prebid_mobile.dart';
```

Then, add `initializeSDK()`method. 
```dart
const PrebidMobile().initializeSDK(ACCOUNT_ID, TIMEOUT, PBSDEBUG)
```
`ACCOUNT_ID` is a placeholder for your Prebid account ID, `TIMEOUT` is a parameter that sets how much time bidders have to submit their bids. It is important to choose a sufficient timeout - if it is too short, there is a chance to get less bids, and if it is too long, it can slow down ad loading and user might wait too long for the ads to appear.
\
`PBSDEBUG` is a boolean type parameter, if it is set to `true`, it adds a debug flag (“test”: 1) into Prebid auction request, which allows to display only test ads and see full Prebid auction response. If none of this required, you can set it to false.

### Geolocation

This plugin already contains all the needed parameters to share user’s location data - location permissions in both Android and iOS, as well as Prebid Mobile SDK’s geolocation flag, which is set to `true`. In order to start collecting this data, you need to add a location permission request to your Flutter app, so that when the user opens your app for the first time, a popup will appear where the user will be able to choose which type of location (precise or approximate) he gives permission to collect (the option to not share the user’s location is also present in the request popup).
