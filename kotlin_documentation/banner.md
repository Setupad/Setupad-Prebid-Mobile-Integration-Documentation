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
In the class or fragment, where the banner will be shown, add a method for banner ad. The context that is passed to this method is the class or fragment where the banner will be displayed. adUnit is a BannerAdUnit object; to initialize it, use `CONFIG_ID`, and the desired banner size, eg. 300x250. The setAutoRefreshInterval is a method for refreshing banners, where the minimum refresh time is 30 seconds. 
BannerParameters are used to customize bid requests. AdManagerAdView is created in accordance with [Google Ad Manager] documentation. Using `addView()`, the banner is attached to the banner slot in the layout file, and, using `fetchDemand()`, a bid request is made to the Prebid Server.
```kotlin
fun createBannerAd(context: Context) {
        adUnit = BannerAdUnit(CONFIG_ID, WIDTH, HEIGHT)
        adUnit?.setAutoRefreshInterval(30)

        val parameters = BannerParameters()
        parameters.api = listOf(Signals.Api.MRAID_3, Signals.Api.OMID_1)
        adUnit!!.bannerParameters = parameters

        val adView = AdManagerAdView(context)
        adView.adUnitId = AD_UNIT_ID
        adView.setAdSizes(AdSize(WIDTH, HEIGHT))
        adView.adListener = createGAMListener(adView)

        val container = findViewById<ViewGroup>(R.id.adManagerAdView)
        container.addView(adView)

        val request = AdManagerAdRequest.Builder().build()
        adUnit?.fetchDemand(request) {
            adView.loadAd(request)
            Log.d(Tag, "Requesting to load banner ad")
        }
    }
```

## Ad listener

Ad listener is used to check whether the ad was successfully loaded. In addition, the `findPrebidCreativeSize` method is used to resize the ad if needed.
```kotlin
private fun createGAMListener(adView: AdManagerAdView): AdListener {
        return object: AdListener() {
            override fun onAdLoaded() {
                super.onAdLoaded()
                AdViewUtils.findPrebidCreativeSize(adView, object: AdViewUtils.PbFindSizeListener {
                    override fun success(width: Int, height: Int) {
                        adView.setAdSizes(AdSize(width, height))
                        Log.d(Tag, "Creative size: " + width + "x" + height)
                    }
                    override fun failure(error: PbFindSizeError) {
                        Log.e(Tag, "Failed to find creative size: " + error.description)
                    }
                })
            }
        }
    }
```

## Destroying ad

Add an `onDestroy()` method for the banner to be destroyed with the activity.
```kotlin
override fun onDestroy() {
   super.onDestroy()
   adUnit?.stopAutoRefresh()
}
```

[Google Ad Manager]: https://developers.google.com/ad-manager/mobile-ads-sdk/android/banner#add_adview
