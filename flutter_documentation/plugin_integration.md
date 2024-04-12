---
title: SDK integration
layout: home
parent: Flutter
nav_order: 2
---

## pubspec.yaml
In your `pubspec.yaml` file’s dependencies add these lines to include Setupad's Prebid plugin for Flutter and run 'flutter pub get' command in terminal.
```yaml
setupad_prebid_flutter:
  git: https://gitlab.com/setupad/prebid_flutter_plugin.git
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
To initialize it, first include this import in your dart file:
```dart
import 'package:setupad_prebid_flutter/prebid_mobile.dart';
```

Then, add this line, where`ACCOUNT_ID` is a placeholder for your Prebid account ID.
```dart
const PrebidMobile().initializeSDK(ACCOUNT_ID)
```