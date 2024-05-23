---
title: Ads
layout: home
parent: Flutter
nav_order: 3
---
The first step is to import ads library.
```dart
import 'package:setupad_prebid_flutter/prebid_ads.dart';
```
Currently, this plugin supports two types of ads: banners and interstitial ads. When adding ad object, you need to specify the ad type, where the ad type can be written in lowercase ("banner"), uppercase ("BANNER") or capitalization ("Banner"). If there will be a typo in the ad type, an error will be shown in the console. 

## Banner
To display a banner, first you need to create a `PrebidAd` class object and then add it to your widget (this is one of the ways it can be done). 
```dart
PrebidAd prebidBanner = const PrebidAd(
  adType: 'banner',
  configId: 'CONFIG_ID',
  adUnitId: 'AD_UNIT_ID',
  width: 300,
  height: 250,
  refreshInterval: 30,
);
//...
@override
  Widget build(BuildContext context) {
    //...
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children:[
            prebidAd, //your banner object added to the widget
            //..
         ],
        ),
      ),
    );
  }
```
`AD_UNIT_ID` and `CONFIG_ID` are placeholders for the ad unit ID and config ID parameters. The minimum refresh interval is 30 seconds, and the maximum is 120 seconds.

## Pausing and resuming auction and destroying banner ad
It is necessary to stop the auction when leaving a screen where the banner ad is displayed because if not stopped, the auction continues happening and displaying ads that are not seen by anyone. This leads to production of incorrect viewability results which can impact the bidding. To avoid all of this, use `PrebidAd` class’ `pauseAuction()` and `resumeAuction` methods. In addition, if there is a need, a banner object can be destroyed using the `destroyAuction` method. 
```dart
prebidBanner.pause();
prebidBanner.resume();
prebidBanner.destroy();
```
To correctly control Prebid auction, you need to use the same object you created for your banner; in this case it is `prebidBanner`.

## Interstitial ad
To display an interstitial ad, you first need to create a `PrebidAd` class object.
```dart
PrebidAd prebidInterstitial = const PrebidAd(
  adType: 'interstitial',
  configId: CONFIG_ID,
  adUnitId: AD_UNIT_D,
  width: 80,
  height: 60,
  refreshInterval: 0,
);
```
`AD_UNIT_ID` and `CONFIG_ID` are placeholders. The refresh interval is set to zero because interstitial ads do not refresh. Unlike in the banner ads, in the interstitial ads the width and height variables are used to indicate the minimum screen's width and height in percent that the interstitial ad can take. In this case 80x60 means that the minimum width of the interstitial ad will be at least 80% of the screen and at least 60% of the height. As these size parameters are optional, you can opt out of specifying them by writing zero as their value. 

If you want to display interstitial ad on button press, it is necessary to use `setState()` with a boolean variable.
```dart
bool _showInterstitial = false;
//...
ElevatedButton(
  child: const Text('Press me!'),
  onPressed: () {
    setState(() {
      _showInterstitial = true;
    });
  },
),
if (_showInterstitial)
  prebidInterstitial,
),
//...
```
