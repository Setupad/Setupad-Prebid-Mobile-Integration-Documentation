---
title: SDK integration
layout: home
parent: Java
nav_order: 2
---

## build.gradle

In your `build.gradle (Module :app)` file’s dependencies include these lines to include Prebid Mobile SDK and Google Ad Manager SDK to the project:
```
dependencies {
    //...
    // Google Ad Manager
    implementation 'com.google.android.gms:play-services-ads:23.0.0'
    // Prebid SDK
    implementation 'org.prebid:prebid-mobile-sdk:2.2.0'
}
```

## AndroidManifest.xml

In the AndroidManifest.xml file add the provided `<uses-permission>` permission tags before the `<application>` tag. 
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```
Location permission, which passes `Lat/Lon` values when they are available, is optional, hence if it is implemented, choose either `ACCESS_COARSE_LOCATION` or `ACCESS_FINE_LOCATION` permission.

<br/>Include the `<meta-data>` tag inside the `<application>` tag with your app ID, provided by Google Ad Manager.
```xml
<application>
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="ca-app-pub-################~##########"/>
    <!--...-->
</application>
```

<br/>Lastly, add a custom activity, which is necessary only for banner and interstitial ads
```xml
<activity
    android:name="org.prebid.mobile.rendering.views.browser.AdBrowserActivity"
    android:configChanges="orientation|screenSize|keyboardHidden"
    android:theme="@android:style/Theme.Translucent.NoTitleBar"
    android:windowSoftInputMode="adjustPan|stateHidden"
    android:launchMode="singleTop"/>
```

## SDK initialization

Prebid Mobile SDK initialization is only needed to be done once. The context passed to the method is the class inside which this method is called. Initialization can be done inside the `onCreate` method by either calling the method that contains the initialization code or without using a separate method. In addition, Prebid can be initialized inside class that extends to `Application` class.
```java
private void prebidInitialization(Context context){
    PrebidMobile.setPrebidServerAccountId("ACCOUNT_ID");
    PrebidMobile.setPbsDebug(false);
    PrebidMobile.setPrebidServerHost(
        Host.createCustomHost(
            "https://prebid.setupad.io/openrtb2/auction"
        )
    );
    
    PrebidMobile.initializeSdk(context, status -> {
        if (status == InitializationStatus.SUCCEEDED)
            Log.d(Tag, "SDK initialized successfully");
        else if (status == InitializationStatus.SERVER_STATUS_WARNING)
            Log.e(Tag, "Prebid Server status checking failed: $status\n${status.description}");
        else
            Log.e(Tag, "SDK initialization error: $status\n${status.description}");
    });
    PrebidMobile.setTimeoutMillis(3000);
    PrebidMobile.setShareGeoLocation(true);
}
```
`ACCOUNT_ID` is a placeholder for Prebid account ID.
The `setTimeoutMillis` sets how much time bidders have to submit their bids. It is important to choose a sufficient timeout - if it is too short, there is a chance to get less bids, and if it is too long, it can slow down ad loading and user might wait too long for the ads to appear. If the `setShareGeoLocation` flag is set to true, then Prebid Mobile will send the user's geolocation to Prebid Server. Setting `setPbsDebug()` to `true` adds a debug flag ("test": 1) into Prebid auction request, which allows to display only test ads and see full Prebid auction response. If none of this required, you can set `pbsDebug()` to false.
