---
layout: post
title:  "Hangit iOS SDK"
date:   2015-05-07 08:47:29
categories: jekyll update
---

<h1>HangIt iOS SDK</h1>

<h2>Overview</h2>

You can use the HangIt platform to leverage location awareness for your iOS application.  

The HangIt SDK monitors and records device location and triggers events when the boundary of a targeted location is crossed.

The SDK also delivers local push notifications and displays rich messages you have configured in HangIt's Campaign Portal.

The SDK allows your application to observe these events and deep link back into the application.

![enter image description here](https://lh3.googleusercontent.com/-3v0YFIrrfVY/VUEys0RemPI/AAAAAAAAA3g/TSzazAEXpT8/s0/hangit.png "hangit.png")

A typical HangIt SDK implementation includes the following:

1. Establishes a session between your app and the SDK, using custom sub-class `ViewController.h*`
2. Enables the HangIt location monitoring service for your app and SDK
3. Enables the app to consume raw JSON campaigns and offers.

*required.


<h2> Getting Started</h2>

>**Latest Version:** The latest version of the HangIt iOS SDK Framework is 1.1.9 and was released May, 7th 2015.

>**Warning:** You will not be able to monitor changes for CoreMotion using the Simulator, you will need to test and run your project on a real device.

In this section we’ll run through the steps to add our Framework to your Xcode project.  If this is your first time out with Xcode and iOS App development, then we recommend you read Apple’s [introductory guide](https://developer.apple.com/library/ios/referencelibrary/GettingStarted/RoadMapiOS/index.html).

There are two possible paths for downloading and installing the HangIt SDK framework for iOS.

 - Clone the HangIt SDK Framework, or
 - Use CocoaPods

>**Note:** CocoaPod installation is available for iOS 8+ only.

<h3>Installing the iOS SDK Framework for Apps Supporting iOS 7+</h3>

<h4>Clone the HangIt SDK Framework</h4>

 - Clone the git repo - git clone https://github.com/hangit/iOS_SDK.git
 - Change to the project folder - cd iOS_SDK/
 - Run the Test project

To add the framework and bundle to your own project goto the section ["Add the SDK Framework files to your project"](https://stackedit.io/editor#add-the-sdk-framework-files-to-your-project)


<h4>Add the SDK Framework files to your project</h4>

 - Create a new project in Xcode and give it a name.
 - Create an empty folder in your project directory.
 - Copy the resource bundle **HangitResources.bundle** to your new folder.
 - Choose **File > Add Files** to Your ProjectName.
 - Select your new folder containing the SDK files
 - Drag the Hangit.framework file to your Embedded Binaries section under **General** for your projects target.


<h4>Link the SDK files to your project</h4>

In XCode 6, the SDK will be linked automatically to your project.

If you need to manually link the files, or just check that they are there, you can use the following procedure.

>**Note:** Optional Step

 - In the project navigator, select your project.
 - Click **Build Phases -> Link Binary With Libraries.**
 - Check that Hangit.framework is already added,

if it isn’t:  

 - Click the **Add button (+).**
 - Select Hangit.framework
 - Click **Add.**

Your project now references the HangIt SDK Framework and your installation is complete.



<h3>Installing the iOS SDK Framework for Apps Supporting iOS 8+</h3>

There are two possible paths for downloading and installing the HangIt SDK framework for iOS 8+.

 - Clone the HangIt SDK Framework, or
 - Use CocoaPods

<h4>Clone the HangIt SDK Framework</h4>

See the section named "Clone the HangIt SDK Framework" above.

<h4>CocoaPods</h4>

 - Create or edit your Podfile
 - Add this to your Podfile: `pod 'Hangit', '~> 1.1.9'


----------

 - Run pod install
 - Open the new CocoaPods workspace for your project and jump to [Session Establishment](https://stackedit.io/editor#session-establishment)

<h4>plist</h4>

For your app to work correctly with iOS 8 you need to add a new key to your project's plist file, and you need to turn on **Background Modes** in Project **Capabilities**

To add a new key to your project’s plist file.

 - In the project navigator, select your project.
 - Select your projects Info.plist file

```
<key>NSLocationAlwaysUsageDescription</key>
<string>Uses background location</string>
```

>The string can be empty, or defined by you the developer, the content is not important.

<h4>Project Capabilities</h4>

To properly setup Project capabilities

 - In the project navigator, select your project.
 - Select your projects **Target** and choose **Capabilities**
 - Turn on Background Modes.
 - Select **"Location updates"** and **"Background fetch"**


----------


<h2>Session Establishment</h2>

As part of establishing a session between your app and the SDK, you will enable your app to receive local push notifications and rich messages.  

Use the HangIt API key generated when you registered on the HangIt Portal (portal.hangit.com) to enable communication between the app and the HangIt service.

To setup this behavior you will create a custom sub-class of `UIViewController` called `SessionManagerDelegate`.  In this tutorial we will simply re-use the sub-class `ViewController.h` for this purpose.

You will need to implement the `sessionManager` method of the custom sub-class as listed below.  You will also perform additional initialization of this method using the `viewDidLoad()` function.

<h3>SDK Framework</h3>

Import the framework to your `ViewController.m` {you may add this within your AppDelegate.m as well} for simplicity, or to any custom sub-class that you define for [UIViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instm/UIViewController/viewDidLoad).

Import the framework using the following code:

```
#import <HangitSDK/HangitSDK.h>
```

{% highlight objective-c %}
#import <HangitSDK/HangitSDK.h>

@interface ViewController : UIViewController <SessionManagerDelegate>

/* Hangit SessionManager */
@property (nonatomic, strong) SessionManager *sessionManager;

/* Hangit MapManager ** Optional, if you want to use the Map feature ** */
@property (nonatomic, strong) MapManager *mapManager;

/* Hangit sessionKey Property */
@property (nonatomic, retain) NSString * sessionKey;
{% endhighlight %}


Declare `SessionManager` inside the [viewDidLoad](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/) function.  This enables the app to receive local push notifications, and present rich messages to users, as indicated in the code below.  Note that startSessionUsingLocation:@"YOURAPIKEY" enables your app to communicate with the HangIt service, and requires your API authentication key.

There are three basic implementation of the Hangit SDK. Each provides a different level of customization for the developer.

 1. Full Message Flow ** RECOMMENDED **
 2. Alert Notification Only
 3. Capture the Trigger on the Location Event Only

To keep things simple... these 5 lines of code are all that are needed to initialize and activate the Hangit Location Services. You can add them within your AppDelegate or MainViewController.

{% highlight objective-c %}
self.sessionManager = [SessionManager sharedInstance];

self.sessionManager.delegate = self;

//Local Push Notification To Users
self.sessionManager.presentNotifications = YES; // If you DO NOT want to display the Alert Notification, Set to NO

//Present Rich Messages To Users
self.sessionManager.presentOfferView = YES;  // If you DO NOT want to display the Rich Message, set to NO

self.sessionKey = [self.sessionManager startSessionUsingLocation:@"YOURAPIKEY"];
{% endhighlight %}

<h2>Congratulations!</h2> You are now using the Hangit Location Services. You manage your location events within the Hangit Management Portal (portal.hangit.com).

<h1>Other Hangit SDK Services </h1>
The Hangit SDK Provides other value-added services in support of your application needs. These are optional and additive features

<h2>Receiving Location Updates</h2>

You can setup your app to consume Location updates from the HangIt SDK.  The HangIt SDK monitors for changes in device location and sends notification to your app.  You can then consume these notifications for your app's location-based needs.

To receive location updates from the HangIt SDK the following is required:

 - include the SDK framework
 - implement the notification method and callback method



<h3>Implementing the Methods</h3>

For the purpose of this example, we will implement methods for the `ViewController.m` sub-class.  You can create your own custom sub-class if you like.

You will implement an Observer method `NSNotificationCenter` that will consume location update notifications received by the HangIt SDK, as illustrated below:

{% highlight objective-c %}
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(locationNotification:) name:@"hangitLocationNotification" object:nil];

- (void) locationNotification:(NSNotification *)notification{

    if ([notification object]) {
        CLLocation * location = [[notification object] objectForKey:@"Location"];
        NSLog(@"location callback: %@", location);
    }
}
{% endhighlight %}

<h2>Receiving Alert Notification Callback</h2>

Application may desire to have a deep-link back into their application after an alert notification is presented. This callback feature allows you to receive the event notification and the notification object.


<h3>Implementing the Methods</h3>

{% highlight objective-c %}
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(messageViewedNotification:) name:@"hangitMessageViewed" object:nil];

- (void) messageViewedNotification:(NSNotification *)notification {

    if ([notification object]) {

        NSLog(@"message viewed callback: %@", [notification object]);

    }
}
{% endhighlight %}

<h3>Callback Method</h3>

You will implement a callback method `didreceiveNotification` in your app's `AppDelegate.m` class, to receive the callback from your app that a HangIt location notification has been consumed.  See the sample below for the implementation:

{% highlight objective-c %}
/* Hangit AppDelegate NotificationManager Requirement */

- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {

    [[NSNotificationCenter defaultCenter] postNotificationName:@"hangitNotificationReceived" object:notification];

}
{% endhighlight %}

<h2>Consuming HangIt Data</h2>

You can setup your app to consume HangIt events and offers via the SDK.

HangIt campaigns include events tied to specific location or "place" that can include rich messages to the user and deep links back to your apps.  HangIt campaigns are provisioned in the HangIt Portal.

HangIt offers are rich messages which can be presented to users when they enter the location bounding area.  The HangIt rich messages are provisioned in the HangIt portal and consist of images, videos or html5 micro sites.

The campaigns and messages are delivered to the app as raw JSON formatted in NSMutable Arrays.

To consume HangIt campaigns and offers, the following is required:

 - Add a `dataService` sub-object to your `ViewController.m` or other custom sub-class you define
 - Implement the callback method

<h3>Implementing the Method</h3>

To setup your app to consume locations, add the `dataservice` sub-object to `ViewController.m` and implement the callback method as below:

{% highlight objective-c %}
CLLocation * myLocation = [[CLLocation alloc] initWithLatitude:28.550 longitude:-81.400];

[DataService instance] getLocationsWithLocation:(myLocation) sessionKey:@"1234-5678-9101" completion:^(NSMutableArray *targetsArray, NSError *error) {
        NSLog(@"%@", targetsArray);
}];

{% endhighlight %}

<h2>HangIt Places Map</h2>

You can implement the HangIt Places Map and display it on your View Controller, to enable users to see a map with all the HangIt Places nearby.

To place the HangIt Places map on your View Controller, implement the `MapManager` method as part of the `ViewController.m` sub class, or other custom sub-class.   The implementation is described below:

<h3>Implementing the Service</h3>

{% highlight objective-c %}
self.mapManager = [[MapManager alloc] initWithNibName:@"MapManager"
        bundle:[NSBundle bundleWithPath:[[NSBundle mainBundle]
        pathForResource:@"HangitSDKResources" ofType:@"bundle"]]];

/* 1. Add the map to the current view and define the frame parameters */

self.mapManager.view.frame = CGRectMake(0,0,320,200);

self.mapManager.view.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;

[self.view addSubview:map.view];
{% endhighlight %}

<h2>HangIt  SDK Settings View</h2>

You can display the HangIt SDK Settings on your View Controller.  You can decide what size you would like the table view frame rect to be, in order to fit with the design of your app.

You will need to implement the `SettingsController` method in your custom sub-class of [UIViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instm/UIViewController/viewDidLoad)

<h3>Implementing the Service</h3>

{% highlight objective-c %}
@property (nonatomic, strong) SettingsController * settingsController;

self.settingsController = [[SettingsController alloc] initWithNibName:@"SettingsController"
    bundle:[NSBundle bundleWithPath:[[NSBundle mainBundle]
    pathForResource:@"HangitSDKResources" ofType:@"bundle"]]];

/* 1. Add the UITableView to the current view and define the frame parameters */

self.settingsController.view.frame = CGRectMake(0,0,320,200);

self.settingsController.view.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;

[self.view addSubview:self.settingsController.view];
{% endhighlight %}
