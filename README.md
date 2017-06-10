# Cashew

[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) ![CocoaPods](https://img.shields.io/cocoapods/v/SwiftyJSON.svg) ![Platform](https://img.shields.io/badge/platforms-iOS%208.0+-333333.svg)

Carthage makes it easy to implement secure messaging into your app.

1. [Requirements](#requirements)
2. [Integration](#integration)
   - [Installation](#installation)
   - [Setting up Xcode](#setting-up-xcode)
3. [Usage](#usage)
   - [Initialization](#initialization)
   - [Subscript](#subscript)
   - [Loop](#loop)
   - [Error](#error)
   - [Optional getter](#optional-getter)
   - [Non-optional getter](#non-optional-getter)
   - [Setter](#setter)
   - [Raw object](#raw-object)
   - [Literal convertibles](#literal-convertibles)
   - [Merging](#merging)
4. [Work with Alamofire](#work-with-alamofire)
5. [Work with Moya](#work-with-moya)


## Requirements

- iOS 8.0+
- Xcode 8

## Integration

### Installation

#### CocoaPods (iOS 8.0+)

You can use [CocoaPods](http://cocoapods.org/) to install `Cashew`by adding it to your `Podfile`:

```ruby
source 'https://github.com/Loment/Specs.git'

platform :ios, '8.0'
use_frameworks!

target 'MyApp' do
	pod 'Cashew'
end
```

Note that this requires CocoaPods version 36, and your iOS deployment target to be at least 8.0:


#### Carthage (iOS 8.0+)

You can use [Carthage](https://github.com/Carthage/Carthage) to install `Cashew` by adding it to your `Cartfile`:

```
github "Loment/sdk-cashew-ios"
```

### Setting Up Xcode

In order to use the Cashew SDK, follow the following steps to set up your Xcode environment:

1. Open project or workspace file after [installing SDK](#installation)
2. In ```Info.plist``` file add the following properties with their appropriate URLs (including port numbers):
```
AMQP_MESSAGE_QUEUE_HOSTNAME
```

```
AMQP_REQUEST_QUEUE_HOSTNAME
```

```
AMQP_RESPONSE_QUEUE_HOSTNAME
```

```
CN_BASE_API_URL
```

```
HTTP_ATTACHMENT_SERVER_URL
```

```
HTTP_DIGEST_REALM
```

3. Include the following lines of code in the ```application:didFinishLaunchingWithOptions:``` method of ```AppDelegate.m```:

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // set up app
    //...
    NSString * clientId = // Please contact Loment Support for value;
    NSString * partnerId = // Please contact Loment Support for value;
    [CNMUserServices registerWithSDK:clientId partnerId:partnerId];
}
```

Once the above steps are done then you are authorized to use the Cashew SDK and its related functionality. For questions or support please contact the Loment team at support@loment.net.

## Usage

