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
Add this object in your dart code where you want do display your banner. 
```dart
const PrebidAd(
  adType: 'banner',
  configId: CONFIG_ID,
  adUnitId: AD_UNIT_D,
  width: 300,
  height: 250,
  refreshInterval: 30,
),
```
`AD_UNIT_ID` and `CONFIG_ID` are placeholders for the ad unit ID and config ID parameters. The minimum refresh interval is 30 seconds.

## Interstitial ad
Add this object to display an interstitial ad.
```dart
const PrebidAd(
  adType: 'interstitial',
  configId: CONFIG_ID,
  adUnitId: AD_UNIT_D,
  width: 80,
  height: 60,
  refreshInterval: 0,
),
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
  const PrebidAd(
    adType: 'interstitial',
    configId: '6145',
    adUnitId: '/147246189/app_test',
    width: 80,
    height: 60,
    refreshInterval: 0,
),
//...
```

## Pausing and resuming auction and destroying banner ad
It is necessary to stop the auction when leaving UI where the banner ad is displayed because if not stopped, the auction continues happening, and displaying ads that are not seen by anyone which produces incorrect viewability results. After coming back to the UI where the auction was stopped, it can be resumed using `PrebidAuction().resume()`. If there is a need, banner object can be destroyed using `PrebidAuction().destroy()` method.

```dart
PrebidAuction().pause();
PrebidAuction().resume();
PrebidAuction().destroy();
```
