---
title: SDK integration
layout: home
parent: Swift
nav_order: 2
---

## Podfile
One of the ways to integrate Prebid Mobile SDK to the project is to use [CocoaPods]. After installing CocoaPods, locate where your `YourProjectName.xcodeproj` file is and using Terminal go to that location. Use the command provided bellow to create Podfile in the location where `YourProjectName.xcodeproj` file is.
```
pod init
```

Open the newly created Podfile, it should look like this:
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
  use_frameworks!
    pod 'PrebidMobile'
    pod 'Google-Mobile-Ads-SDK'
```
Optionally, you can define a global platform for your project.

Then using the terminal, install Prebid Mobile SDK and Google Ad Manager SDK using the command bellow.
```
pod install
```
If the installation is successfull, you will find `YourProjectName.xcworkspace` file located in the same folder where 'YourProjectName.xcodeproj' is.

## Info.plist
Open the newly created `YourProjectName.xcworkspace` file. Then, in your project structure under your project, find `Info.plist` file, it should be located like this: `YourProjectName -> YourProjectName -> Info`. It can be shown in hierarchy just as `Info`.
Inside Info.plist file, include the GADApplicationIdentifier. It is optional to include [SKAdNetworkItems] items.
```xml
<key>GADApplicationIdentifier</key>
<string>ca-app-pub-################~##########</string>
```

## SDK initialization
In the class where the banner will be shown, include these imports:
```swift
import PrebidMobile
import GoogleMobileAds
```

Make sure that your class adheres to `GADBannerViewDelegate` protocol, e.g.
```swift
class ViewController: UIViewController, GADBannerViewDelegate {
```

Prebid Mobile SDk initialization is only needed to be done once. It can either be done inside a class method or a separate function.
```swift
func prebidInit(){
        Prebid.shared.prebidServerAccountId = ACCOUNT_ID;
        Prebid.shared.pbsDebug = true
        Prebid.shared.shareGeoLocation = true
        
        do {
            try Prebid.shared.setCustomPrebidServer(url:"https://prebid.setupad.io/openrtb2/auction")
        } catch {
            print( \(error))
        }
        
        Prebid.initializeSDK(GADMobileAds.sharedInstance()) { status, error in
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
    }
```
If the ShareGeoLocation flag is set to true, then Prebid Mobile will send the user's geolocation to Prebid Server.

[CocoaPods]: https://cocoapods.org/
[SKAdNetworkItems]: https://developers.google.com/ad-manager/mobile-ads-sdk/ios/quick-start#expandable-1  
