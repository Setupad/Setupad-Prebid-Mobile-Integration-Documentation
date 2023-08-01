---
title: SDK integration
layout: home
parent: Kotlin
nav_order: 2
---

## build.gradle

In your build.gradle (Module :app) file’s dependencies include these lines to include Prebid Mobile SDK and Google Ad Manager SDK to the project:
```Groovy
dependencies {
    //...
    // Google Ad Manager
    implementation 'com.google.android.gms:play-services-ads:22.2.0'
    // Prebid SDK
    implementation 'org.prebid:prebid-mobile-sdk:2.1.1'
}
```

## AndroidManifest.xml

In the AndroidManifest.xml file include the `<meta-data>` tag inside the `<application>` tag. 
```xml
<application
        <!--...--> >
        <meta-data
            android:name="com.google.android.gms.ads.APPLICATION_ID"
            android:value="ca-app-pub-################~##########"/>
        <!--...-->
    </application>
```

## SDK initialization

Prebid Mobile SDK initialization is only needed to be done once. The context passed to the method, is the class or fragment, inside which this method is called. Initialization can also be done inside the onCreate method without using a separate method as in the example provided below.
```Kotlin
fun prebidInit(context: Context){
        PrebidMobile.setPrebidServerAccountId(ACCOUNT_ID)
        PrebidMobile.setPrebidServerHost(
            Host.createCustomHost(
                "https://prebid.setupad.io/openrtb2/auction"
            )
        )
        PrebidMobile.setPbsDebug(true)
        PrebidMobile.initializeSdk(context) { status ->
            if (status == InitializationStatus.SUCCEEDED) {
                Log.d(Tag, "SDK initialized successfully!")
            } else if (status == InitializationStatus.SERVER_STATUS_WARNING) {
                Log.e(Tag, "Prebid Server status checking failed: $status\n${status.description}")
            }
            else {
                Log.e(Tag, "SDK initialization error: $status\n${status.description}")
            }
        }
        PrebidMobile.setTimeoutMillis(3000)
        PrebidMobile.setShareGeoLocation(true)
    }
```
If the ShareGeoLocation flag is set to true, then Prebid Mobile will send the user's geolocation to Prebid Server.


