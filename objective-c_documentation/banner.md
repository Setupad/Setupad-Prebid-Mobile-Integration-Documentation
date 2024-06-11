---
title: Banner
layout: home
parent: Objective-C
nav_order: 3
---

## Banner
In the `MyClass.m`, where the banner will be shown, add a function for banner ad. Banner parameters are used to customize the bid request. The values that can be choosen from are `PBApi.MRAID_1`, `PBApi.MRAID_2`, `PBApi.MRAID_3` and `PBApi.OMID_1`.
`BannerParameters` are used to customize bid requests. 
```objc
self.adSize = CGSizeMake(300, 250);
///...
- (void)createBanner {
    self.bannerAdUnit = [[BannerAdUnit alloc] initWithConfigId:CONFIG_ID size:self.adSize];
    [self.bannerAdUnit setAutoRefreshMillisWithTime:30000];

    BannerParameters * parameters = [[BannerParameters alloc] init];
    parameters.api = @[PBApi.MRAID_3];
    self.bannerAdUnit.bannerParameters = parameters;

    GAMRequest * gamRequest = [GAMRequest new];
    self.gamBanner = [[GAMBannerView alloc] initWithAdSize:GADAdSizeFromCGSize(self.adSize)];
    self.gamBanner.adUnitID = AD_UNIT_ID;
    self.gamBanner.rootViewController = self;
    self.gamBanner.delegate = self;
    [self addBannerViewToView:_gamBanner];

    @weakify(self);
    [self.bannerAdUnit fetchDemandWithAdObject:gamRequest completion:^(enum ResultCode resultCode) {
        @strongify(self);
        if (!self) { return; }
        [self.gamBanner loadRequest:gamRequest];
    }];
}
```
`bannerAdUnit` is a `BannerAdUnit` object; to initialize it, use `CONFIG_ID`, and the desired banner size, e.g. 300x250. The `setAutoRefreshMillisWithTime` is a method for refreshing banners, where the minimum refresh time is 30 seconds, and the maximum is 120 seconds.
`BannerParameters` are used to customize bid requests. Using `addBannerViewToView()` function, the banner is attached to the layout, and, using `fetchDemand`, a bid request is made to the Prebid Server.

## Adding a banner element to the layout
The banner is added to the view using the `addBannerViewToView()` function, to which the banner object is passed. In the provided code below, the banner is added at the bottom of the layout and centered horizontally.
```objc
- (void)addBannerViewToView:(UIView *)bannerView {
    bannerView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addSubview:bannerView];
    [self.view addConstraints:@[
        [NSLayoutConstraint constraintWithItem:bannerView
                                     attribute:NSLayoutAttributeBottom
                                     relatedBy:NSLayoutRelationEqual
                                        toItem:self.view.safeAreaLayoutGuide
                                     attribute:NSLayoutAttributeBottom
                                    multiplier:1
                                      constant:0],
        [NSLayoutConstraint constraintWithItem:bannerView
                                     attribute:NSLayoutAttributeCenterX
                                     relatedBy:NSLayoutRelationEqual
                                        toItem:self.view
                                     attribute:NSLayoutAttributeCenterX
                                    multiplier:1
                                      constant:0]
    ]];
}
```

## Ad listeners
Ad listeners are used to check whether the ad was successfully loaded. In addition, the `findPrebidCreativeSize` method is used to resize the ad if needed. In order for the listeners to work properly, they have to be added after the function that contains the banner code.
```objc
- (void)bannerViewDidReceiveAd:(GADBannerView *)bannerView {
    [AdViewUtils findPrebidCreativeSize:bannerView success:^(CGSize size) {
        [self.gamBanner resize:GADAdSizeFromCGSize(size)];
    } failure:^(NSError * _Nonnull error) {
        PBMLogError(@"Error occured during search for Prebid creative size: %@", error.localizedDescription)
    }];
}

- (void)bannerView:(GADBannerView *)bannerView didFailToReceiveAdWithError:(NSError *)error {
    PBMLogError(@"Failed to load banner ad: %@", error.localizedDescription)
}
```
## Pausing and resuming auction
It is necessary to stop the auction when leaving an activity where the banner ad is displayed because if not stopped, the auction continues happening, and displaying ads that are not seen by anyone which produces incorrect viewability results. After coming back to the view where the auction was stopped, it can be resumed using `resumeAutoRefresh`.
```objc
- (void)viewDidDisappear:(BOOL)animated{
    [super viewDidDisappear:(BOOL)animated];
    [self.bannerAdUnit stopAutoRefresh];
}

- (void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:(BOOL)animated];
    [self.bannerAdUnit resumeAutoRefresh];
}
```
