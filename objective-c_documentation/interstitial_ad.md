---
title: Interstitial ad
layout: home
parent: Swift
nav_order: 4
---


## Interstitial ad
To show an interstitial ad, there is no need to add an element to the layout where the ad will be shown. `interstitialAdUnit` is an `InterstitialAdUnit` object; to initialize it, use `CONFIG_ID`. It is optional to specify the minimum height and width in percent of the ad, e.g. 80x60 means that the minimum width of the interstitial ad will be at least 80% of the screen and at least 60% of the height. If the ad is successfully loaded, it will be shown on the screen, otherwise an error will be printed.
```objc
- (void)createInterstitialAd{
    self.interstitialAdUnit = [[InterstitialAdUnit alloc] initWithConfigId: CONFIG_ID minWidthPerc:60 minHeightPerc:80];
    
    GAMRequest *gamRequest = [GAMRequest new];
    @ weakify(self);
    [self.interstitialAdUnit fetchDemandWithAdObject:gamRequest completion:^(enum ResultCode resultCode){
        @strongify(self);
        if(!self) {return;}
        @weakify (self);
        [GAMInterstitialAd loadWithAdManagerAdUnitID:AD_UNIT_ID request:gamRequest completionHandler:^(GAMInterstitialAd * _Nullable interstitialAd, NSError *_Nullable error){
            @strongify(self);
            if(!self) {return;}
            if(error != nil){
                PBMLogError(@"Failed to load interstitial ad with error: %@", error.localizedDescription);
            } else if (interstitialAd != nil){
                interstitialAd.fullScreenContentDelegate = self;
                [interstitialAd presentFromRootViewController:self];
            }
        }];
    }];
}
```
