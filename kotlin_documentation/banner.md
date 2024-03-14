---
title: Banner
layout: home
parent: Kotlin
nav_order: 3
---


## Adding a banner element to the layout

In the layout file, associated with the class or fragment where the banner will be shown, ad this `AdManagerAdView`. 
```xml
<com.google.android.gms.ads.admanager.AdManagerAdView
    android:id="@+id/adManagerAdView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_centerHorizontal="true"
    app:adSize="300x250"
    app:adUnitId="AD_UNIT_ID"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"/>
```
The banner will be constrained to the bottom of the layout and centered horizontally. Ad size and ad unit ID are necessary for the banner to be shown.

## Banner 
In the class or fragment, where the banner will be shown, add a method for banner ad. Banner parameters are used to customize the bid request. The values that can be choosen from are `Signals.Api.MRAID_1`, `Signals.Api.MRAID_2`, `Signals.Api.MRAID_3` and `Signals.Api.OMID_1`.
```kotlin
private var bannerAdUnit: BannerAdUnit ? = null

//...

private fun createBannerAd(context: Context){
    bannerAdUnit = BannerAdUnit(CONFIG_ID, WIDTH, HEIGHT)
    val parameters = BannerParameters()
    parameters.api = listOf(Signals.Api.MRAID_3, Signals.Api.OMID_1)
    bannerAdUnit?.bannerParameters = parameters
    
    bannerAdUnit?.setAutoRefreshInterval(getRefreshTimeSeconds())
    
    val gamView = AdManagerAdView(context)
    gamView.adUnitId = AD_UNIT_ID
    gamView.setAdSizes(AdSize(WIDTH, HEIGHT))
    
    val container = findViewById<ViewGroup>(R.id.adManagerAdView)
    container.addView(gamView)
    gamView.adListener = bannerListener(gamView)
    
    val request = AdManagerAdRequest.Builder().build()
    
    bannerAdUnit?.fetchDemand(request) {
        gamView.loadAd(request)
    }
}

private fun getRefreshTimeSeconds(): Int {
    return 30
}
```
The context that is passed to this method is the class or fragment where the banner will be displayed. `adUnit` is a `BannerAdUnit` object; to initialize it, use `CONFIG_ID`, and the desired banner size, eg. 300x250. The `setAutoRefreshInterval` is a method for refreshing banners, where the minimum refresh time is 30 seconds. 
`BannerParameters` are used to customize bid requests. `AdManagerAdView` is created in accordance with [Google Ad Manager] documentation. Using `addView`, the banner is attached to the banner slot in the layout file, and, using `fetchDemand`, a bid request is made to the Prebid Server.

## Ad listener

Ad listener is used to check whether the ad was successfully loaded. In addition, the `findPrebidCreativeSize` method is used to resize the ad if needed.
```kotlin
private fun bannerListener(gamView: AdManagerAdView): AdListener {
    return object : AdListener() {
        override fun onAdLoaded() {
            super.onAdLoaded()
            Log.d(Tag, "Banner ad loaded successfully")
            AdViewUtils.findPrebidCreativeSize(gamView, object : AdViewUtils.PbFindSizeListener {
                override fun success(width: Int, height: Int) {
                    gamView.setAdSizes(AdSize(width, height))
                    Log.d(Tag, "Creative size: " + width + "x" + height)
            }
            
                override fun failure(error: PbFindSizeError) {
                    Log.e(Tag, "Failed to find creative size: " + error.description)
                }
            })
        }
        
        override fun onAdFailedToLoad(adError: LoadAdError) {
            Log.e(Tag, "Failed to load banner ad: " + adError.message)
        }
    }
}
```

## Pausing and resuming auction
It is necessary to stop the auction when leaving an activity where the banner ad is displayed because if not stopped, the auction continues happening, and displaying ads that are not seen by anyone which produces incorrect viewability results. After coming back to the activity where the auction was stopped, it can be resumed using `resumeAutoRefresh()`.
```kotlin
override fun onStop() {
    super.onStop()
    Log.d(Tag, "Pausing auction")
    bannerAdUnit?.stopAutoRefresh()
}

override fun onPause() {
    super.onPause()
    Log.d(Tag, "Pausing auction")
    bannerAdUnit?.stopAutoRefresh()
}


override fun onResume() {
    super.onResume()
    Log.d(Tag, "Resuming auction")
    bannerAdUnit?.resumeAutoRefresh()
}
```

## Destroying ad

Add an `onDestroy()` method for the banner to be destroyed with the activity.
```kotlin
override fun onDestroy() {
    super.onDestroy()
    Log.d(Tag, "Pausing auction")
    bannerAdUnit?.stopAutoRefresh()
    bannerAdUnit?.destroy()
}
```

[Google Ad Manager]: https://developers.google.com/ad-manager/mobile-ads-sdk/android/banner#add_adview
