---
title: Interstitial ad
layout: home
parent: Swift
nav_order: 4
---


## Interstitial ad
To show an interstitial ad, there is no need to add an element to the layout file of the class or fragment where the ad will be shown. interstitialAdUnit is an InterstitialAdUnit object; to initialize it, use CONFIG_ID. It is optional to specify the minimum height and width percent of the ad, e.g. 80x60. If the ad is successfully loaded, it will be shown.
```swift
func createInterstitialAd(){
        interstitialAdUnit = InterstitialAdUnit(configId: CONFIG_ID, minWidthPerc: 60, minHeightPerc: 80)
        
        let gamRequest = GAMRequest()
        interstitialAdUnit.fetchDemand(adObject: gamRequest) { [weak self] resultCode in
            print("Prebid demand fetch for GAM \(resultCode.name())")

            GAMInterstitialAd.load(withAdManagerAdUnitID: AD_UNIT_ID, request: gamRequest) { ad, error in
                guard let self = self else { return }
                if let error = error {
                    print("Failed to load interstitial ad with error: \(error.localizedDescription)")
                } else if let ad = ad {
                    ad.fullScreenContentDelegate = self
                    ad.present(fromRootViewController: self)
                }
            }
        }
    }

```
