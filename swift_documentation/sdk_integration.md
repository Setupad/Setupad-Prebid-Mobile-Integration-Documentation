---
title: SDK integration
layout: home
parent: Swift
nav_order: 2
---

## Podfile
One of the ways to integrate Prebid Mobile SDK into the project is to use [CocoaPods]. After installing CocoaPods, locate where your `YourProjectName.xcodeproj` file is, and using Terminal go to that location. Use the command provided below to create `Podfile` in the location where the `YourProjectName.xcodeproj` file is.
```
pod init
```

Open the newly created `Podfile`, it should look like this:
```ruby
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'YourProjectName' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for YourProjectName

  target 'YourProjectNameTests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'YourProjectNameUITests' do
    # Pods for testing
  end

end
```

Inside the target `YourProjectName` and after the `use_frameworks!` line include these two lines, save the file and then close it.
```ruby
pod 'PrebidMobile'
pod 'Google-Mobile-Ads-SDK'
```

Using the terminal, install Prebid Mobile SDK and Google Ad Manager SDK using the command below.
```
pod install
```
If the installation is successful, you will find the `YourProjectName.xcworkspace` file located in the same folder where the 'YourProjectName.xcodeproj' is.

## Info.plist
Open the newly created `YourProjectName.xcworkspace` file. Then, in your project structure under your project, find the `Info.plist` file, it should be located in Project navigator in XCode like this: `YourProjectName -> YourProjectName -> Info`. It can be shown in the navigator just as `Info` instead of `Info.plist`.
Inside the Info.plist file, include the `GADApplicationIdentifier`. It is optional to include [SKAdNetworkItems] items.
```xml
<key>GADApplicationIdentifier</key>
<string>ca-app-pub-################~##########</string>
```

\
For better ad targeting and ad revenue, it is recommended to pass user’s geolocation to the Prebid Server. The first step in doing so is to add location permission to your `Info.plist` file. `NSLocationWhenInUseUsageDescription` let’s user choose from a coarse and fine location (the option to not share the user's location is also present in the request popup). In addition, you need to provide the description for what purposes you need to access the user's location data.
```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>The text you want to display in the popup asking for user’s permission to collect their location data</string>
```

\
In addition, when using the Prebid Mobile SDK, your app collects user IDFA (The Identifier for Advertisers). As per [Apple policy], starting iOS 14.5, if your app is collecting IDFA, you need to use App Tracking Transparency framework which requests user’s permission to access their IDFA. To do so, include `NSUserTrackingUsageDescription` key to your Info.plist file and add Swift code that is responsible for requesting user’s permission and handling it. 
```xml
<key>NSUserTrackingUsageDescription</key>
<string>The text you want to display in the popup asking for user's permission to collect their IDFA</string>
```

## SDK initialization
In the class where the banner will be shown, include these imports:
```swift
import PrebidMobile
import GoogleMobileAds
```

Make sure that your class adheres to the `GADBannerViewDelegate` protocol.
```swift
class MyClass: GADBannerViewDelegate {
```

Prebid Mobile SDK initialization is only needed to be done once. It can either be done inside a class method or a separate function.
```swift
func prebidInitialization(){
    Prebid.shared.prebidServerAccountId = ACCOUNT_ID
    Prebid.shared.pbsDebug = false
            
    do {
        try Prebid.shared.setCustomPrebidServer(url:"https://prebid.setupad.io/openrtb2/auction")
    } catch {
        print( \(error))
    }
            
    Prebid.initializeSDK(gadMobileAdsVersion: GADGetStringFromVersionNumber(GADMobileAds.sharedInstance().versionNumber)) { status, error in
        switch status {
            case .succeeded:
                print("Prebid SDK successfully initialized")
            case .failed:
                if let error = error {
                    print("An error occurred during Prebid SDK initialization: \(error.localizedDescription)")
                }
            case .serverStatusWarning:
                if let error = error {
                    print("Prebid Server status checking failed: \(error.localizedDescription)")
                }
            default:
                break
        }
    }
    Prebid.shared.shareGeoLocation = true
    Prebid.shared.timeoutMillis = 3000
}
```
`ACCOUNT_ID` is a placeholder for Prebid account ID.
The `setTimeoutMillis` sets how much time bidders have to submit their bids. It is important to choose a sufficient timeout - if it is too short, there is a chance to get less bids, and if it is too long, it can slow down ad loading and user might wait too long for the ads to appear. 
\
\
The second step of sharing the user’s location is setting the `shareGeoLocation` flag to true and the final step is asking user’s permission to collect location data. After doing these 3 steps (permissions in the plist file, setting location flag to true and asking user for permission) Prebid Mobile SDK will send the user's device location to the Prebid Server.
\
\
Setting `setPbsDebug()` to `true` adds a debug flag ("test": 1) into Prebid auction request, which allows to display only test ads and see full Prebid auction response. If none of this required, you can set `pbsDebug()` to false.

[CocoaPods]: https://cocoapods.org/
[SKAdNetworkItems]: https://developers.google.com/ad-manager/mobile-ads-sdk/ios/quick-start#expandable-1  
[Apple policy]: https://developer.apple.com/app-store/user-privacy-and-data-use/ 
