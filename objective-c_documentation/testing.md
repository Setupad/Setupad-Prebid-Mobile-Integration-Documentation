---
title: Testing
layout: home
parent: Objective-C
nav_order: 5
---

In order to test whether Prebid Mobile SDK and ads were successfully integrated into the mobile application, we provide testing parameters. If the integration was successful, using the provided test parameters, you should see PubMatic test ads that look like this:

<img width="330" alt="image1" src="https://github.com/Setupad/Setupad-Prebid-Mobile-Integration-Documentation/assets/140802751/3393a5ae-2ae9-4464-a78c-50f7761ef371">   
<img width="330" alt="image2" src="https://github.com/Setupad/Setupad-Prebid-Mobile-Integration-Documentation/assets/140802751/3ba2971b-5e3a-4897-9429-77bb9d006fe2">

If the testing is done using a simulator, on top of the ad will be visible a ‘Test mode’ writing, as can be seen in the provided example. If the testing is done on a physical device, this writing will not be visible.

Testing parameters for iOS:
* ACCOUNT_ID: `apptest`
* AD_UNIT_ID: `/147246189/app_test`
* CONFIG_ID for 300x250 banner: `6139`
* CONFIG_ID for interstitial ad: `6141`
