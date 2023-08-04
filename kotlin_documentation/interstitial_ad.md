---
title: Interstitial ad
layout: home
parent: Kotlin
nav_order: 4
---


## Interstitial ad
To show an interstitial ad, there is no need to add an element to the layout file of the class or fragment where the ad will be shown. The context that is passed to `createInterstitialAd` method is the class or fragment where the banner will be displayed. `interstitialAdUnit` is an `InterstitialAdUnit` object; use `CONFIG_ID`. It is optional to specify the minimum height and width percent of the ad, e.g. 80x60.
```kotlin
fun createInterstitialAd(context: Context) {
        interstitialAdUnit = InterstitialAdUnit(CONFIG_ID, WIDTH, HEIGHT)

        val request = AdManagerAdRequest.Builder().build()
        interstitialAdUnit?.fetchDemand(request) {
            AdManagerInterstitialAd.load(
                context,
                AD_UNIT_ID,
                request,
                createListener()
            )
        }
    }
```

## Ad listener

Ad listener is used to check whether the ad was successfully loaded.
```kotlin
private fun createListener(): AdManagerInterstitialAdLoadCallback {
   return object: AdManagerInterstitialAdLoadCallback() {
       override fun onAdLoaded(adManagerInterstitialAd: AdManagerInterstitialAd) {
           super.onAdLoaded(adManagerInterstitialAd)
           adManagerInterstitialAd.show(this@MyClass)
           Log.d(Tag, "Ad was loaded and shown")
       }
       override fun onAdFailedToLoad(loadAdError: LoadAdError) {
           super.onAdFailedToLoad(loadAdError)
           Log.e(Tag, "Ad failed to load: $loadAdError")
       }
   }
```
