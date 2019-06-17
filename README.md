

## [![Netcore Logo](https://netcore.in/wp-content/themes/netcore/img/Netcore-new-Logo.png)](http:www.netcore.in) Xamarin iOS SDK Integration

Complete steps to integrate and get started with Smartech Xamarin iOS SDK.

## Introduction

Smartech provides an Xamarin iOS SDK that enable app developers to track and engage their users and view valuable analytical insights on our powerful Smartech dashboard.  
This guide will show you how to install the Smartech Xamarin iOS SDK in your Xamarin iOS project.

## Prerequisites

##### 1. A Smartech Account. 
##### 2. A Smartech Xamarin iOS  `App Id`  which can be obtained from the Smartech panel.
##### 3. An active Apple developer account. If you don’t have one, you can enroll it from  [Apple Developer Program](https://developer.apple.com/programs/enroll/). 
##### 4. A Mac system with a minimum version of Xcode 9 or above.
##### 5. A valid push notification certificate which has to be exported to a .p12 file with a password.
##### 6. An iOS device (iPhone, iPad, iPod Touch) to test the push notifications. The Xcode simulator doesn't support push notifications so it must be tested on a real device.

## Getting Started:

To use the SDK into your project you can have 2 approaches:

-   Adding the SDK via  `NuGet Package Manager`
-   Manually adding the  `Smartech.dll`

###  Manually adding the NetcoreSDK.framework

##### 1.  You can download the Netcore Smartech SDK from our  [GitHub repository](https://github.com/NetcoreSolutions/Smartech-ios-sdk)  or  [GitHub Master Direct Link](https://github.com/NetcoreSolutions/Smartech-ios-sdk/archive/master.zip).
##### 2. Unzip the downloaded file.
##### 3. Add .dll file to your project -> Right click References -> Edit References -> .Net Assembly                         -> Browse -> Smartech.dll.

## Configuring App Entitlements & Extensions

### Enabling App Entitlements
For our SDK to work in your project you need to enable below app capabilities in your main app.

- `App Groups`  - Enable and select a valid  `App Group`  for your app.
- `Background Modes`  - Enable and select  `Remote notifications`.
- `Push Notifications` - Enable and Configure App Push Notifications feature for  
your  `App ID`  in Apple Developer Account.

### Adding Notification Service Extension

1) Add “Notification Service Extension” to your app. Right Click Solution->
Add ->Project- >Notification Service Extension.

2) Click Next.

3) Enable and Add “App Groups” to your apps Entitlements(Add one group with name "<group.com.CompanyName.ProductName>"), here in the example we are using SmartechNSE..

4) Enable and Add App groups in Service Extension too and select group with name "<group.com.CompanyName.ProductName>".

5) Set the  **`deployment target`**  to minimum of  **`10.0`**.


### Changes In Notification Service Extension


### Implementation Changes
You need to add the following changes in the Notification Service Extension's implementation file. `NotificationService.cs`

1) Add NetCore Framework into Extension

```swift
using NetCorePush
```

2) Handle Notification Request

```swift
public  override  void  DidReceiveNotificationRequest(UNNotificationRequest  request,  Action<UNNotificationContent>  contentHandler)
{
NetCoreNotificationService.SharedInstance().SetUpAppGroup(<app_group_name>);
NetCoreNotificationService.SharedInstance().DidReceiveNotificationRequest(request,  contentHandler);
}
```

3) Handle Notification Service Time Expire

```swift
public  override  void  TimeWillExpire()
{
NetCoreNotificationService.SharedInstance().TimeWillExpire();
}
```

## Initialize Smartech SDK

### AppDelegate Changes

1. Import the header files.
You need to import the following header files in your `AppDelegate` file

```swift
using  NetCorePush;  
using  UserNotifications;  
```

2. Setup the Smartech App ID and App Group

```swift
var  appGroup  =  "group.com.yourcompanyname.productname";  
var  netCoreAppId  =  "APP ID THAT YOU GET FROM SMARTECH PANEL";
```

3. Add code in 'FinishedLaunching' method.

```swift
NetCoreSharedManager.SharedInstance().SetUpAppGroup(<App_Group>);
if  (launchOptions  !=  null)
{
    NetCoreSharedManager.SharedInstance().HandleApplicationLaunchEvent(launchOptions,  <App_Id>);
}
else
{
    NetCoreSharedManager.SharedInstance().SetUpApplicationId(<App_Id>);
}
```

4.  Instantiate and initialize delegate classes for custom push notifications.
```swift
NetCorePushTaskManager.SharedInstance().Delegate  =  new  NetcoreCustomDelegate(navController);
UNUserNotificationCenter.Current.Delegate  =  new  CustomUNUserNotificationCenterDelegate();
var  arr  =  new  NSObject[]  {  new  NSString("https://abc...xyz")  };
NetCoreSharedManager.SharedInstance().SetAssociateDomain(arr);
```

5. Add following code in ‘RegisteredForRemoteNotifications’ method of ‘AppDelegate.cs’ file.
```swift
NetCoreInstallation.SharedInstance().NetCorePushRegisteration(<Email_Id>,  <Device_Token>);
NetCoreSharedManager.SharedInstance().PrintDeviceToken();
```

6. Add following code in ‘DidReceiveRemoteNotification’ method of ‘AppDelegate.cs’ file.
```swift
NetCorePushTaskManager.SharedInstance().DidReceiveRemoteNotification(<NSDictionary_UserInfo>);
```

7. Add following code in ‘ReceivedLocalNotification’ method of ‘AppDelegate.cs’ file.
```swift
NetCorePushTaskManager.SharedInstance().DidReceiveLocalNotification(<UILocalNotification_Notification>);
```

8. Add Location Permission services in Info.plist
```swift
<key>NSLocationWhenInUseUsageDescription</key>
<string> App requires your location.</string>
<key>NSLocationAlwaysUsageDescription</key>
<string> App requires your location.</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string> App requires your location.</string>
```

9. Add below code to handle location updates.
```swift
var  locMgr  =  new  CLLocationManager();
if  (UIDevice.CurrentDevice.CheckSystemVersion(8,  0))
{
    locMgr.RequestAlwaysAuthorization();
}
```

## User Profile

### Introduction

Smartech SDK enable developers to identify a user and create a profile for the user with all its attributes. Assigning a unique user identity helps collect the user's activity and information across systems in a single unified view.
**`Unique user identity must be your primary key to identify users in your database.`**
We recommend not to use any user identity that can change over time.
Normally you can set the user identity in the following flow of your app.
-   On user sign-in/login success.
-   On user sign-up/register success.
-   On screen or page where user identity becomes known.
-   When the user context changes (apps with multiple accounts).

### Setting User Identity
```swift
NetCoreSharedManager.SharedInstance().SetUpIdentity(<Identity>);
```

### Login
Ensure you call the Smartech SDK's `NetCorePushLogin` method as soon as the user login to your application or whenever the user's identity is known.  
In the below code you need to pass the identity of the user.

```swift
NetCoreInstallation.SharedInstance().NetCorePushLogin(<Identity>)
```
### Profile Push
There are cases where additional information associated with a user can be passed as user attributes. These attributes can be used to segment users and target campaigns to a specific group. Attributes can also be used to send more personalize campaign messages to each user.

You can use the below code to profile push.  
**NOTE: All the keys in the payload dictionary should be in capital letters**

```swift
var  keys  =  new  object[]  {  "NAME",  "EMAIL",  "AGE"};
var  values  =  new  object[]  {"Netcore","netcore@netcore.co.in",21};
var  payload  =  NSDictionary.FromObjectsAndKeys(values,  keys);
NetCoreInstallation.SharedInstance().NetCoreProfilePush(<identity>,<payload>)
```
### Clear Identity
To clear the user's identity you need to explicitly call  `clearIdentity`  method.

**`This method clears the identity locally and all the event carried out after this call will be treated as Anonymous user activity.`**

```swift
NetCoreSharedManager.SharedInstance().ClearIdentity();
```
### Logout
Ensure you call the logout method `NetCorePushLogout` on successfully logging out the user.  
To logout the user you need to call the `NetCorePushLogout` method. When you logout the user, you won't be receiving any push notifications.
```swift
NetCoreInstallation.SharedInstance().NetCorePushLogout();
```
## Event Tracking

### Introduction

Events are all the actions performed by the user on the mobile application. Smartech SDK enables you to record user events and later learn more about your app’s usage patterns and to segment your users, target and personalize messaging based on the events performed by them.  
Each event must have a name and a set of attributes describing more about the event in detail.

### Tracking Custom Events

-  Tracking By Event Name
```swift
NetCoreAppTracking.SharedInstance().TrackEventWithCustomPayload(<event_name>,  <payload>);

// Sample Code For Reference
NSObject keys = new NSString("payload");
NSObject values = new NSString(<payload>);
var  payload = NSMutableDictionary.FromObjectAndKey(values,  keys);
var  payloadArray = new  NSMutableDictionary();
payloadArray.Add(keys, values);
NetCoreAppTracking.SharedInstance().TrackEventWithCustomPayload(<event_name>,  payloadArray);
```


## Advanced

### To Setting Universal Link
You need to pass the universal link value to the SDK that you have added in your Capabilities -> Associated Domains section Eg. applinks:https://www.netcoresmartech.com then enter only domain name as [https://www.netcoresmartech.com](https://www.netcoresmartech.com/), you can pass multiple domain names.

```swift
NetCoreSharedManager.SharedInstance().SetAssociateDomain(<universal-link>);
```
### To Handle Deep Link and Custom Payload (2.3.6 onwards)

```swift
public  override  void  HandleSmartechDeeplink(SMTDeeplink  smtDeeplink)
{
if (smtDeeplink.DeepLinkType  ==  SMTDeeplinkType.Url)
{
// When Deeplink is WebURL
}
else if (smtDeeplink.DeepLinkType  ==  SMTDeeplinkType.UniversalLink)
{
// When Deeplink is Universal-link
}
else if (smtDeeplink.DeepLinkType  ==  SMTDeeplinkType.Deeplink)
{
// When Deeplink is URLSchemes link
}

//To handle custom payload add your relevant code.
if(smtDeeplink.CustomPayload  !=  null)
{
}
}
```
### Fetch Delivered Push Notifications

At times you may want to create your own notification center to display all the past notification delivered to the user's device.

-  Fetch All Push Notifications
To display all push notifications delivered to a user then you can call `Notifications`method. This method will return `NSArray`.

```swift
NSObject[] msgArray = NetCoreSharedManager.SharedInstance().Notifications;
```
### Get Notification With Limit
Sometimes showing all the past push notifications is irrelevant, to limit the push notification to be fetched you can call `GetNotificationsWithCount` method and pass the count. This will fetch all the latest 'n' number of notifications.

```swift
NSObject[] msgArray = NetCoreSharedManager.SharedInstance().GetNotificationsWithCount(<count>);
```

### GDPR Compliance
Smartech believes privacy is a fundamental human right, which is why Smartech SDK has `OptOut` method, once called will stop tracking user's events, displaying of In App Messages and receiving push notifications sent from the panel.

- Opt Out From Tracking
To opt-out of tracking call the `OptOut` method by passing **`true`** value. Once you have opted out of tracking you need to explicitly opt in, until opted in no communication will happen with the panel.

```swift
NetCoreSharedManager.SharedInstance().OptOut(<boolean_flag>);
```

- Opt In For Tracking
To opt-in for tracking, call the `OptOut` method by passing **`false`** value.
```swift
NetCoreSharedManager.SharedInstance().OptOut(<boolean_flag>);
```

### Get Device Token

To get the device token which is used by APNs to send Push Notifications.

```swift
NetCoreSharedManager.SharedInstance().DeviceToken;
```

### Print Device Token
To print the device token in your console.

```swift
NetCoreSharedManager.SharedInstance().PrintDeviceToken();
```
### Get GUID
To get the GUID which is used by Smartech SDK to identify the user, you can call the `GUID` method

```swift
NetCoreSharedManager.SharedInstance().GUID;
```
