---
title: Banner
layout: home
parent: Java
nav_order: 3
---


## Adding a banner element to the layout

In the layout file, associated with the activity where the banner will be shown, ad this `AdManagerAdView` element. 
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
`AD_UNIT_ID` is a placeholder for ad unit ID. The banner will be constrained to the bottom of the layout and centered horizontally. Ad size and ad unit ID are necessary for the banner to be shown.

## Banner 
In the class, associated with the activity where the banner will be shown, add a method for banner ad. Banner parameters are used to customize the bid request. The values that can be choosen from are `Signals.Api.MRAID_1`, `Signals.Api.MRAID_2`, `Signals.Api.MRAID_3` and `Signals.Api.OMID_1`.
```java
public void createBannerAd(Context context) {
    bannerAdUnit = new BannerAdUnit(CONFIG_ID, WIDTH, HEIGHT);
    BannerParameters parameters = new BannerParameters();
    parameters.setApi(Arrays.asList(Signals.Api.OMID_1));
    bannerAdUnit.setBannerParameters(parameters);
    
    bannerAdUnit.setAutoRefreshInterval(getRefreshTimeSeconds());
    
    final AdManagerAdView gamView = new AdManagerAdView(context);
    gamView.setAdUnitId(AD_UNIT_ID);
    gamView.setAdSizes(new AdSize(WIDTH, HEIGHT));
    
    ViewGroup container = findViewById(R.id.adManagerAdView);
    container.addView(gamView);
    gamView.setAdListener(bannerListener(gamView));
    
    final AdManagerAdRequest.Builder bannerBuilder = new AdManagerAdRequest.Builder();
    
    bannerAdUnit.fetchDemand(bannerBuilder, resultCode -> {
        AdManagerAdRequest request = bannerBuilder.build();
        gamView.loadAd(request);
    });
}

private int getRefreshTimeSeconds() {
    return 30;
}
```
The context that is passed to this method is the class where the banner will be displayed. `bannerAdUnit` is a `BannerAdUnit` object; to initialize it, use `CONFIG_ID`, and the desired banner size, eg. 300x250. The `setAutoRefreshInterval` is a method for refreshing banners, where the minimum refresh time is 30 seconds. You can either use a method to set refresh time (like the provided `getRefreshTimeSeconds()`), or simply write the desired refresh time. 
`BannerParameters` are used to customize bid requests. `AdManagerAdView` is created per [Google Ad Manager] documentation. Using `addView`, the banner is attached to the banner slot in the layout file, and, using `fetchDemand`, a bid request is made to the Prebid Server.

## Ad listener

Ad listener is used to check whether the ad was successfully loaded. In addition, the `findPrebidCreativeSize` method is used to resize the ad.
```java
private AdListener bannerListener(AdManagerAdView gamView) {
    return new AdListener() {
        @Override
        public void onAdLoaded() {
            Log.d(Tag, "Banner ad loaded successfully");
            AdViewUtils.findPrebidCreativeSize(gamView, new AdViewUtils.PbFindSizeListener() {
                @Override
                public void success(int width, int height) {
                    gamView.setAdSizes(new AdSize(width, height));
                    Log.d(Tag, "Creative size: " + width + "x" + height);
                }
                
                @Override
                public void failure(@NonNull PbFindSizeError error) {
                    Log.e(Tag, "Failed to find creative size: " + error.getDescription());
                }
            });
        }
        @Override
        public void onAdFailedToLoad(@NonNull LoadAdError adError) {
            Log.e(Tag, "Failed to load banner ad: " + adError.getMessage());
        }
    };
}
```
## Pausing and resuming auction
It is necessary to stop the auction when leaving an activity where the banner ad is displayed because if not stopped, the auction continues happening, and displaying ads that are not seen by anyone which produces incorrect viewability results. After coming back to the activity where the auction was stopped, it can be resumed using `resumeAutoRefresh()`.
```java
@Override
protected void onStop() {
    super.onStop();
    if (bannerAdUnit != null) {
        Log.d(Tag, "Pausing auction");
        bannerAdUnit.stopAutoRefresh();
    }
}

@Override
protected void onPause() {
    super.onPause();
    if (bannerAdUnit != null) {
        Log.d(Tag, "Pausing auction");
        bannerAdUnit.stopAutoRefresh();
    }
}

@Override
protected void onResume() {
    super.onResume();
    if (bannerAdUnit != null) {
        Log.d(Tag, "Resuming auction");
        bannerAdUnit.resumeAutoRefresh();
    }
}
```

## Destroying ad

Add an `onDestroy()` method for the banner to be destroyed with the activity.
```java
@Override
protected void onDestroy() {
    super.onDestroy();
    if (bannerAdUnit != null) {
        bannerAdUnit.stopAutoRefresh();
        bannerAdUnit = null;
    }
}
```

[Google Ad Manager]: https://developers.google.com/ad-manager/mobile-ads-sdk/android/banner#add_adview
