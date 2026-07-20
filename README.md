# 🚀 Getting Started

This guide is intended for **Engineering teams** within a publisher who wish to integrate with the **Prebid Mobile SDK API version 1.0**. The current version supports **Banner** and **Interstitial** ad formats.

---

### What is Prebid?

*   **Prebid Mobile** is an end-to-end open-source *header bidding* solution (for Android and iOS) designed for mobile app publishers.
*   **Prebid Server** is a server-to-server open-source *header bidding* solution that allows publishers to configure multiple header bidding demand partners to work through a server connection.

Setting up Prebid Server and integrating the Prebid Mobile SDK into an application is the first crucial step toward displaying ads and generating revenue.

---

## ⚙️ Overview: The Impression Technical Flow

The following is the technical flow of an impression when using the Prebid Mobile SDK with an ad server. Note that for each additional adapter, steps 3 through 6 would be repeated.

1.  **App Initiation:** The client application initiates the ad unit(s).
2.  **SDK Request:** The Prebid Mobile SDK interprets the code for the ad unit and sends an OpenRTB (version 2.5) HTTP **POST** request, containing an Account ID and Configuration ID, to the Rubicon hosted Prebid Server.
3.  **Bidder Mapping:** Prebid Server maps the received Account ID and Configuration ID to determine the eligible bidders and applies their respective settings to each ad unit.
4.  **Demand Request:** The eligible Demand Adapter (e.g., Rubicon, AppNexus) submits a request for an ad to its corresponding Ad Exchange endpoint.
5.  **Auctioning:** The Ad Exchanges submit an OpenRTB bid request to their Buyers (DSPs). One or more Buyers may submit a bid response back to the Ad Exchange. The Ad Exchange performs an auction and submits a winning ad back to Prebid Server (via the Demand Adapter).
    *   *Crucial:* At this time, the ad creative is simultaneously stored and cached in the Prebid Server for retrieval upon ad render.
6.  **SDK Preparation:** Prebid Server translates each adapter’s bid into a key-value pair to be passed to the Prebid Mobile SDK. It receives key-values for (1) a bid and (2) a creative URL, which it then attaches to the ad request for the Ad Server SDK.
7.  **Ad Server Call:** The Ad Server SDK sends an ad request to the Ad Server. The parameters must match the targeting previously configured on the line items.
8.  **Ad Selection:** The ad selection process occurs within the Ad Server. If a Prebid line item wins, the Ad Server serves the default Prebid JavaScript creative that includes a reference to the creative URL.
9.  **Display:** The JavaScript creative fetches the actual creative content from the Prebid Cache Service, and the ad renders for the user.

---

## ✅ Integration Checklist

| Item | Scope | Status |
| :--- | :--- | :--- |
| Receive ad unit names and configs from ad ops | Required | ⬜ |
| Register apps-ads.txt entries | Highly Recommended | ⬜ |
| **iOS Code Integration** | | |
| Import the Prebid Mobile SDK | Required | ⬜ |
| Configure Prebid Server URL | Required | ⬜ |
| Configure Global Account ID | Required | ⬜ |
| Setup and create ad units | Required | ⬜ |
| Privacy Regulation | Recommended | ⬜ |
| Prepare ad unit for fetch | Required | ⬜ |
| Resize ad unit | Recommended | ⬜ |
| **Android Code Integration** | | |
| Import the Prebid Mobile SDK | Required | ⬜ |
| Configure Prebid Server URL | Required | ⬜ |
| Configure Global Account ID | Required | ⬜ |
| Setup and create ad units | Required | ⬜ |
| Privacy Regulation | Recommended | ⬜ |
| Prepare ad unit for fetch | Required | ⬜ |
| Resize ad unit | Recommended | ⬜ |

---

## 📄 Account Setup Details

### 🔹 Account ID (Mandatory Field)

The Prebid Mobile SDK requires an account within the Magnite Demand Manager service.

*   **Scenario 1: Using Rubicon**
    If you are using Rubicon as a bidder through an alternate Prebid Server host, please contact your host service provider for documentation or refer to the Prebid Mobile site for setup.
*   **What the ID Does:** The Account ID for Demand Manager maps to the wrapper-level stored request ID. This ID is used both to identify your account and to retrieve global Prebid level attributes (like currency and price granularity).
*   **Format:** Because the Account ID serves multiple roles, it is possible to have more than one Prebid Account ID / wrapper-level store request ID for your integration.
*   **Required Format:** Magnite Wrapper-Level Stored Requests IDs (Prebid Account IDs) must always follow this pattern:

    ```
    <RP account ID>-<wrapper-level-stored-request-name>
    ```
    **Example:**
    `1001-NewsApps`

*   **Application:** After your Ad Ops team registers the Account within the Magnite platform, this ID must be applied to all Prebid Mobile SDK requests (for ads), in both iOS and Android setups.

### 🔹 Ad Unit Configs

The Prebid Mobile SDK requires each ad slot to have a unique ad unit configuration. All Ad Unit configs must be collected before starting your integration process with your Ad Ops team. If you do not have an Ad Ops team, please contact your Magnite Account team for steps to obtain the required Ad Unit configs.

### 📰 Registering `apps-ads.txt`

To ensure no future revenue loss to your ads, we strongly recommend setting all demand partner entries in your `apps-ads.txt` file hosted on your app's website. If you don't have a dedicated app website, many companies offer free web hosting.

*   Detailed information can be found on the IAB Tech Labs site or on Magnite's resource page.
*   Your Ad Ops team or Magnite Account Manager will provide any entries required for Rubicon.

---

## 💻 Platform Integration

### 🍎 iOS Code Integration

**1. Import the Prebid Mobile SDK**
The iOS source code is hosted under the Prebid organization on GitHub.
[IOS SDK REPO](https://github.com/prebid/prebid-mobile-ios)


**A. Via CocoaPods (Recommended)**
The simplest way is using CocoaPods. Open your project's [CocoaPods](https://guides.cocoapods.org/using/getting-started)  and add this line to your app's target:

```ruby
# Ensure you set the platform version (e.g., '9.0')
platform :ios, '9.0'
target 'MyAmazingApp' do
    pod 'PrebidMobile'
end
```

**B. From Source Build**
You can also build from source code. Run this command from the root directory after cloning the repository to generate [IOS SDK REPO](https://github.com/prebid/prebid-mobile-ios):

```bash
./scripts/buildPrebidMobile.sh
```

## 🚀 Initialize the Prebid SDK

Once you have set the account ID, the next crucial step is to initialize the Prebid SDK.

**Important Note for SDK 3.0+**
For SDK version 3.0 and later, you must provide the full URL to your Prebid Server's auction endpoint within your application code. You should obtain this URL from your Prebid Server provider.

**Example URL:** `https://prebid-server.example.com/openrtb2/auction`



### Implementation Example Android

Use the following initialization method for the Prebid SDK:

```swift
PrebidMobile.initializeSdk(applicationContext, PREBID_SERVER_URL) { status ->
    if (status == InitializationStatus.SUCCEEDED) {
        Log.d(TAG, "SDK initialized successfully!")
    } else if (status == InitializationStatus.SERVER_STATUS_WARNING) {
        Log.e(TAG, "Prebid Server status checking failed: $status\n${status.description}")
    } else {
        Log.e(TAG, "SDK initialization error: $status\n${status.description}")
    }
}
```

More information can be found here [Prebid Android](https://docs.prebid.org/prebid-mobile/pbm-api/android/code-integration-android.html#initialize-sdk)

### 🧩 Integration with GMA SDK IOS

If you are integrating Prebid Mobile with the **Google Mobile Ads (GMA) SDK** at version 10.7.0 or higher, you must use the specialized initializer. This function automatically checks the compatibility between the Prebid SDK and the GMA SDK used in your app.

#### 🛠️ Initialization Code Example

```swift
Prebid.initializeSDK(
    PREBID_SERVER_URL, 
    gadMobileAdsVersion: String(for: MobileAds.shared.versionNumber)
) { status, error in
    switch status {
    case .succeeded:
        print("✅ Prebid SDK successfully initialized")
    case .failed:
        if let error = error {
            print("❌ An error occurred during Prebid SDK initialization: \(error.localizedDescription)")
        }
    case .serverStatusWarning:
        if let error = error {
            print("⚠️ Prebid Server status checking failed: \(error.localizedDescription)")
        }
    default:
        break
    }            
}
```
More information can be found here [Prebid IOS](https://docs.prebid.org/prebid-mobile/pbm-api/ios/code-integration-ios.html#initialize-sdk)


**2. Configure Global SDK Attributes**

| Attribute | SDK v3.0 and Above | SDK Below v3.0 |
| :--- | :--- | :--- |
| **Prebid Server URL** | The use of `Host.RUBICON` is **deprecated**. You must explicitly pass the URL using the new `initializeSdk()` method. | Developers should set the Magnite hosted Prebid Server URL as shown below: |

**🔗 Core URLs (Mandatory to Update):**

*   **`initializeSdk()` Example:** (Check official docs for platform-specific implementation)
*   **Android:** `https://prebid-server.rubiconproject.com/openrtb2/auction`
*   **iOS:** (See official prebid documentation)

> **⚠️ Important:** The `initializeSdk()` method is required in SDK v3.0 and above to guarantee that the Prebid SDK is initialized correctly before ads are loaded.

**3. Configure Account ID**
The global Account ID is mandatory and must be attached to all ad requests (fetches). Refer to the **Account ID setup section** for detailed setup instructions.

```swift
// Example (Swift/iOS)
// Sets the wrapper-level stored request ID.
Prebid.shared.prebidServerAccountId = "1001-Top-Game-App" 
```

### 🏗️ Ad Unit Setup and Creation

There are four high-level steps in the setup and creation of an ad unit:

1.  Create ad unit(s), which include: Ad Unit IDs, Config IDs, and Sizes.
2.  Set targeting parameters for the ad units (optional).
3.  Apply any privacy regulations as required (optional).
4.  Prepare ad units for ads (required).
5.  Resize ad unit (recommended for GAM integrations).

**Best practice:** Create and register Prebid Mobile ad units as early as possible in the application’s lifecycle.

#### Ad Unit Types

Demand Manager supports the following Ad Unit types (Note: Multiformat ad requests are **not** supported in the Prebid SDK). Each Ad Unit type is a sub-class of the Prebid SDK’s Ad Unit class.

**Banner Ad Units**
The Banner Ad Unit requires two parameters:
*   **Config ID:** The Ad Unit Pattern Stored Request ID in the Demand Manager UI.
*   **Sizes:** The width and height of the requested ad (`CGSize`).

```swift
// Code Example (Conceptual)
let bannerUnit = BannerAdUnit(configId: "1001-level1-banner", size: CGSize(width: 300, height: 250))

// To add additional sizes:
bannerUnit.addAdditionalSizes(sizes: CGSize(width: 320, height: 50))

// For code examples or further details, please reference the Prebid.org site for Banner.
```