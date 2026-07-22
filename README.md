# Getting Started

This guide is intended for Engineering teams within a publisher who wish to integrate with the Prebid Mobile SDK API version 3.0. Please note that the current version supports **Banner** and **Interstitial** ad formats.

**Prebid Mobile** is an end-to-end open-source *header bidding* solution for mobile app publishers on both Android and iOS. **Prebid Server**, conversely, is a server-to-server open-source *header bidding* solution that allows publishers to configure select demand partners to operate through a server connection.

By setting up Prebid Server and integrating the Prebid Mobile SDK into an application, you take the critical first step toward displaying ads and generating revenue.


## iOS Code Integration
___

### Import the Prebid Mobile SDK

The iOS source code is hosted under the Prebid organization on [Github](https://github.com/prebid/prebid-mobile-ios).

### CocoaPods
The simplest way to import the SDK into an iOS project is with [CocoaPods](https://guides.cocoapods.org/using/getting-started). Open your project’s Podfile and add this line to your app’s target:

Replace ‘MyAmazingApp’ with your app name
```text
platform :ios, '9.0'
target 'MyAmazingApp' do
    pod 'PrebidMobile'
end
```

Optional Build from source

You can also build the Prebid Mobile SDK from [source code](https://github.com/prebid/prebid-mobile-ios). Run this command from the root directory after cloning the repository to output the PrebidMobile.Framework:
```text
./scripts/buildPrebidMobile.sh
```
### Configure Global SDK Attributes

#### Configure Prebid Server URL
#### SDK v3.0 and Above
Starting in Prebid SDK v3.0, the use of Host.RUBICON is deprecated. 
The Prebid Server URL must be explicitly passed using the new initializeSdk() method.

##### Initialize SDK
Once you set the account ID, you should initialize the Prebid SDK.

In SDK 3.0 and later, you need to enter a URL to your Prebid Server’s auction endpoint in your app. 
Get this URL from your Prebid Server provider. e.g. https://prebid-server.example.com/openrtb2/auction.

Use the following initialization for Prebid SDK:

```text
PrebidMobile.initializeSdk(applicationContext, PREBID_SERVER_URL) { status ->
    if (status == InitializationStatus.SUCCEEDED) {
        Log.d(TAG, "SDK initialized successfully!")
    } else if (status == InitializationStatus.SERVER_STATUS_WARNING) {
        Log.e(TAG, "Prebid Server status checking failed: $status\n${status.description}")
    }
    else {
        Log.e(TAG, "SDK initialization error: $status\n${status.description}")
    }
}
```
Replace "PREBID_SERVER_URL" with
```text
https://prebid-server.rubiconproject.com/openrtb2/auction
```
⚠️ Important: The initializeSdk() method is required in SDK v3.0 and above to ensure the Prebid SDK is properly initialized before loading ads. Please make sure to use the Prebid links above for the correct process to initialize the SDK per platform.
---
### Configure Account ID
The global Account ID is a required field attached to all requests (fetches) for ads. Refer to the Account ID setup section for further detail.
```text
Prebid.shared.prebidServerAccountId = "INSERT-ACCOUNT-ID";
```

The Account ID (set as the wrapper-level stored request ID in the Demand Manager UI) value will contain a set of numbers prepended with a configuration name. The Demand Manager Service will utilize those numbers to identify you as a publisher. For this reason, there may be use cases to have more than one Account ID to store your global attributes across ad requests if you require more than one global configuration.

Example Account ID:
```text
Prebid.shared.prebidServerAccountId = "1001-Top-Game-App"
```

### Setup and create ad units

The creation of an ad unit follows these four high-level steps:

1. **Create Ad Unit(s):** Define the core attributes, including Ad Unit IDs, Config IDs, and required Sizes.
2. **Set Targeting Parameters:** Apply specific targeting parameters to the ad units (Optional).
3. **Apply Privacy Regulations:** Implement necessary privacy regulations (Optional).
4. **Prepare and Configure Ad Units:** Prepare the ad units for live ad serving. (Resizing the ad unit is highly recommended for GAM integrations).

> **💡 Best Practice:** It is best practice to create and register Prebid Mobile ad units as early as possible in the application's lifecycle.


### Create Ad Units
Demand Manager supports any of the following Ad Unit types (multiformat ad requests are not supported in the Prebid SDK). 
Each Ad Unit type is a sub-class of the [Prebid SDK’s Ad Unit class](https://docs.prebid.org/prebid-mobile/prebid-mobile.html).

### Banner ad units

The Banner Ad Unit takes two parameters:

	➡️ Config ID (String): This is the Ad Unit Pattern Stored Request ID in the Demand Manager UI

	➡️ Sizes (CGSize): Width and height of the requested ad

```text
let bannerUnit = BannerAdUnit(configId: "1001-level1-banner", size: CGSize(width: 300, height: 250))

```
To add additional sizes:
```text
bannerUnit.addAdditionalSizes(sizes: CGSize(width: 320, height: 50))

```
For code examples or further details, please reference the Prebid.org site for Banner.

____


## Android Code Integration
___

### Import the Prebid Mobile SDK

The Android source code is hosted under the Prebid organization on [Github](https://github.com/prebid/prebid-mobile-android).

#### Maven
If you are not familiar with using Maven for build management visit the [Maven website](https://maven.apache.org/index.html). 
Open the app-level build.gradle file for your app and look for “dependencies”. Add the following line, which instructs Gradle to pull in the latest 1.x version of the Prebid Mobile SDK:
```text
implementation 'org.prebid:prebid-mobile-sdk:1.0'
```

#### Build from source

You can also build the Prebid Mobile SDK from [source code](https://github.com/prebid/prebid-mobile-android). Run this command from the root directory after cloning the repository. It is recommended to use Java version  8+.

```text
export ANDROID_HOME=<path-to-Android-sdk>
./buildprebid.sh
```
##### Initialize SDK
Once you set the account ID, you should initialize the Prebid SDK.

In SDK 3.0 and later, you need to enter a URL to your Prebid Server’s auction endpoint in your app.
Get this URL from your Prebid Server provider. e.g. https://prebid-server.example.com/openrtb2/auction.

Use the following initialization for Prebid SDK:

```text
PrebidMobile.initializeSdk(applicationContext, PREBID_SERVER_URL) { status ->
    if (status == InitializationStatus.SUCCEEDED) {
        Log.d(TAG, "SDK initialized successfully!")
    } else if (status == InitializationStatus.SERVER_STATUS_WARNING) {
        Log.e(TAG, "Prebid Server status checking failed: $status\n${status.description}")
    }
    else {
        Log.e(TAG, "SDK initialization error: $status\n${status.description}")
    }
}
```
Replace "PREBID_SERVER_URL" with
```text
https://prebid-server.rubiconproject.com/openrtb2/auction
```
#### Configure Global SDK attributes
Configure Prebid Server URL
Developers should explicitly set the Rubicon hosted Prebid Server URL PrebidMobile.java file located in:

Example:
```text
PrebidMobile.setPrebidServerHost(Host.RUBICON);
```

#### Configure Global Account ID
The global Account ID is a required field attached to all requests (fetches) for ads. Refer to the Account ID setup section for further detail.
```text
PrebidMobile.setPrebidServerAccountId("INSERT-ACCOUNT-ID");
```
The Account ID (set as the wrapper-level stored request ID in the Demand Manager UI) value will contain a set of numbers prepended with a configuration name. The Demand Manager Service will utilize those numbers to identify you as a publisher. For this reason, there may be use cases to have more than one Account ID to store your global attributes across ad requests if you require more than one global configuration.

Example Account ID:
```text
PrebidMobile.setPrebidServerAccountId = "1001-Top-Game-App"
```
#### Setup and create ad units
The setup and creation of an ad unit is a four-step process:

*   **Create Ad Unit(s):** Define necessary identifiers: Ad Unit IDs, Config IDs, and Sizes.
*   **Set Targeting Parameters:** Configure specific targeting criteria for the ad units (Optional).
*   **Apply Privacy Attributes:** Apply any required user privacy regulations (Optional).
*   **Prepare Ad Units:** Finalize the units for ad serving.

> **Best Practice:** Always aim to create and register Prebid Mobile ad units as early as possible in the application's lifecycle.

#### Create Ad Units

##### Banner Ad Units

Below is a sample definition of a Banner Ad Unit creation with one additional ad size.
```text
//Create the ad unit(s) - this is an example for a Banner ad unit
BannerAdUnit adUnit1 = new BannerAdUnit("PREBID_SERVER_CONFIGURATION_ID", 300, 250);
```
#### Banner Ad Units
The Banner Ad Unit takes two parameters:

	➡️ Config ID (String): This is the Ad Unit Pattern Stored Request ID in the Demand Manager UI
	➡️ Sizes (CGSize): Width and height of the requested ad
```text
BannerAdUnit bannerAdUnit = new BannerAdUnit("PREBID_SERVER_CONFIGURATION_ID", 300, 250);
```
To add additional sizes:
```text
bannerUnit.addAdditionalSizes(sizes: CGSize(width: 320, height: 50))
```
For code examples or further details please reference the Prebid.org site for Banner








