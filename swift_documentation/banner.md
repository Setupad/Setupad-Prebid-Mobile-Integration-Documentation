---
title: Banner
layout: home
parent: Swift
nav_order: 3
---


## Adding a banner element to the layout
The banner is added to the view using the `addbannerViewToView` function, to which the banner object is passed. This function should be included in the same file where the `createBanner` function is. In the provided code below, the banner is added at the bottom of the layout and centered horizontally.
```swift
    func addBannerViewToView(_ bannerView: GAMBannerView) {
        bannerView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(bannerView)
        view.addConstraints(
            [NSLayoutConstraint(item: bannerView,
                                attribute: .bottom,
                                relatedBy: .equal,
                                toItem: view.safeAreaLayoutGuide,
                                attribute: .bottom,
                                multiplier: 1,
                                constant: 0),
             NSLayoutConstraint(item: bannerView,
                                attribute: .centerX,
                                relatedBy: .equal,
                                toItem: view,
                                attribute: .centerX,
                                multiplier: 1,
                                constant: 0)
            ])
    }
}
```
## Banner
In the class, where the banner will be shown, add a function for banner ad. `adUnit` is a `BannerAdUnit` object; to initialize it, use `CONFIG_ID`, and the desired banner size, e.g. 300x250. The `setAutoRefreshMillis` is a method for refreshing banners, where the minimum refresh time is 30 seconds. 
`BannerParameters` are used to customize bid requests. 
```swift
let adSize = CGSize(width: 300, height: 250)
///...
func createBanner(){
        adUnit = BannerAdUnit(configId: CONFIG_ID, size: adSize)
        adUnit.setAutoRefreshMillis(time: 30000)

        let parameters = BannerParameters()
        parameters.api = [Signals.Api.MRAID_2]
        adUnit.bannerParameters = parameters

        gamBanner = GAMBannerView(adSize: GADAdSizeFromCGSize(adSize))
        gamBanner.adUnitID = AD_UNIT_ID
        gamBanner.rootViewController = self
        gamBanner.delegate = self
        addBannerViewToView(gamBanner)
        
        let gamRequest = GAMRequest()
        adUnit.fetchDemand(adObject: gamRequest) { [weak self] resultCode in
            print("Prebid demand fetch for GAM \(resultCode.name())")
            self?.gamBanner.load(gamRequest)
        }
    }
```

## Ad listener
Ad listener is used to check whether the ad was successfully loaded. In addition, the `findPrebidCreativeSize` method is used to resize the ad if needed.
```swift
    func bannerViewDidReceiveAd(_ bannerView: GADBannerView) {
        AdViewUtils.findPrebidCreativeSize(bannerView, success: { size in
            guard let bannerView = bannerView as? GAMBannerView else { return }
            bannerView.resize(GADAdSizeFromCGSize(size))
        }, failure: { (error) in
            print("Error occuring during searching for Prebid creative size: \(error)")
        })
    }
```
