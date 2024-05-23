---
title: App Tracking Transparency
layout: home
parent: Flutter
nav_order: 4
---

Starting iOS 14+, it is necessary to ask user for permission to track their data, which is necessary for better ads targeting. In case user refuses to be tracked, IDFA is set to zeroes. More about App Tracking Transparency can be read [here].
One of the ways to ask for user's permission is to use App Tracking Transparency [plugin], made specifically for this purpose. 

## Info.plist
The first step is adding these lines to your Info.plist file (the same one where Google Ad Manager app ID was added) to display a tracking authorization request when user downloads your app and opens it for the first time. Inside the `<string>` tags add the text you want to display in the request.
```xml
<key>NSUserTrackingUsageDescription</key>
<string>We use your data for advertising purposes to improve our services.</string>
```

## pubspec.yaml
In your `pubspec.yaml` file dependencies include this dependency:
```yaml
app_tracking_transparency: ^2.0.4
```
## Plugin's implementation

This is an example code on how this plugin can be integrated into your app. By adding it, a request popup will be visible to the user. 

Note: for the request popup to appear, it is necessary to display additional dialog using `showCustomTrackingDialog` method. More about this can be read in [plugin]'s description.
```dart
import 'package:app_tracking_transparency/app_tracking_transparency.dart';

//...

class _MyAppState extends State<MyAppState> {
  String _authStatus = 'Unknown';

  @override
  void initState() {
    super.initState();

    WidgetsFlutterBinding.ensureInitialized()
        .addPostFrameCallback((_) => initPlugin());
  }

  Future<void> showCustomTrackingDialog(BuildContext context) async =>
      await showDialog<void>(
        context: context,
        builder: (context) =>
            AlertDialog(
              title: const Text('Permission to track data'),
              content: const Text(
                "Text, explaining in more detail on why your app needs to permission to track user's data."
              ),
              actions: [
                TextButton(
                  onPressed: () => Navigator.pop(context),
                  child: const Text('Continue'),
                ),
              ],
            ),
      );

  Future<void> initPlugin() async {
    final TrackingStatus status = await AppTrackingTransparency
        .trackingAuthorizationStatus;
    if (status == TrackingStatus.notDetermined) {
      await showCustomTrackingDialog(context);
      await Future.delayed(const Duration(milliseconds: 200));
      await AppTrackingTransparency.requestTrackingAuthorization();
      setState(() => _authStatus = '$status');
    }
  }
  //The rest of your code
}
```

----
[here]: https://developer.apple.com/documentation/apptrackingtransparency
[plugin]: https://pub.dev/packages/app_tracking_transparency
