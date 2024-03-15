---
title: Interstitial ad
layout: home
parent: Java
nav_order: 4
---


## Interstitial ad
To show an interstitial ad, there is no need to add an element to the layout file of the class where the ad will be shown. The context that is passed to `createInterstitialAd` method is the class where the interstitial ad will be displayed. `interstitialAdUnit` is an `InterstitialAdUnit` object; use `CONFIG_ID`. It is optional to specify the minimum height and width in percent of the ad, e.g. 80x60 means that the minimum width of the interstitial ad will be at least 80% of the screen and at least 60% of the height.
```java
private void createInterstitialAd() {
    interstitialAdUnit = new InterstitialAdUnit(CONFIG_ID, WIDTH, HEIGHT);
    
    final AdManagerAdRequest.Builder builder = new AdManagerAdRequest.Builder();
    interstitialAdUnit.fetchDemand(builder, resultCode -> {
        AdManagerAdRequest request = builder.build();
        AdManagerInterstitialAd.load(this, AD_UNIT_ID, request, interstitialListener());
    });
}
```

## Ad listener

Ad listener is used to check whether the ad was successfully loaded.
```java
private AdManagerInterstitialAdLoadCallback interstitialListener() {
    return new AdManagerInterstitialAdLoadCallback() {
        @Override
        public void onAdLoaded(@NonNull AdManagerInterstitialAd interstitialManager) {
            Log.d(Tag, "Interstitial ad loaded successfully");
            interstitialManager.show(MyClass.this);
        }
        
        @Override
        public void onAdFailedToLoad(@NonNull LoadAdError loadAdError) {
            Log.e(Tag, "Failed to load interstitial ad: "+ loadAdError.getMessage());
        }
    };
}
```
