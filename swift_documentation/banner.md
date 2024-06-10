---
title: Banner
layout: home
parent: Swift
nav_order: 3
---

## Banner
In the class, where the banner will be shown, add a function for banner ad. Banner parameters are used to customize the bid request. The values that can be choosen from are `Signals.Api.MRAID_1`, `Signals.Api.MRAID_2`, `Signals.Api.MRAID_3` and `Signals.Api.OMID_1`.
`BannerParameters` are used to customize bid requests. 
```swift
let adSize = CGSize(width: 300, height: 250)
///...
func createBanner(){
    bannerAdUnit = BannerAdUnit(configId: CONFIG_ID, size: adSize)
    bannerAdUnit.setAutoRefreshMillis(time: 30000)
    
    let parameters = BannerParameters()
    parameters.api = [Signals.Api.OMID_1, Signals.Api.MRAID_3]
    bannerAdUnit.bannerParameters = parameters
    
    gamBanner = GAMBannerView(adSize: GADAdSizeFromCGSize(adSize))
    gamBanner.adUnitID = AD_UNIT_ID
    gamBanner.rootViewController = self
    gamBanner.delegate = self
    addBannerViewToView(gamBanner)
            
    let gamRequest = GAMRequest()
    bannerAdUnit.fetchDemand(adObject: gamRequest) { [weak self] resultCode in
        print("Prebid demand fetch for GAM \(resultCode.name())")
        self?.gamBanner.load(gamRequest)
    }
}
```
`bannerAdUnit` is a `BannerAdUnit` object; to initialize it, use `CONFIG_ID`, and the desired banner size, e.g. 300x250. The `setAutoRefreshMillis` is a method for refreshing banners, where the minimum refresh time is 30 seconds, and the maximum is 120 seconds.
`BannerParameters` are used to customize bid requests. Using `addBannerViewToView()` function, the banner is attached to the layout, and, using `fetchDemand`, a bid request is made to the Prebid Server.

## Adding a banner element to the layout
The banner is added to the view using the `addBannerViewToView()` function, to which the banner object is passed. In the provided code below, the banner is added at the bottom of the layout and centered horizontally.
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
```

## Ad listener
Ad listener is used to check whether the ad was successfully loaded. In addition, the `findPrebidCreativeSize()` method is used to resize the ad if needed. In order for the listener to work properly, it has to be added after the function that contains the banner code.
```swift
func bannerViewDidReceiveAd(_ bannerView: GADBannerView) {
    AdViewUtils.findPrebidCreativeSize(bannerView, success: { size in
        guard let bannerView = bannerView as? GAMBannerView else { return }
        bannerView.resize(GADAdSizeFromCGSize(size))
    }, failure: { (error) in
        print("Error occured during search for Prebid creative size: \(error)")
    })
}
```
## Pausing and resuming auction
It is necessary to stop the auction when leaving an activity where the banner ad is displayed because if not stopped, the auction continues happening, and displaying ads that are not seen by anyone which produces incorrect viewability results. After coming back to the activity where the auction was stopped, it can be resumed using `resumeAutoRefresh()`.
```swift
override func viewDidDisappear(_ animated: Bool) {
    print ("Pausing auction")
    bannerAdUnit?.stopAutoRefresh()
}
    
override func viewWillAppear(_ animated: Bool) {
    print ("Resuming auction")
    bannerAdUnit?.resumeAutoRefresh()
}
```
