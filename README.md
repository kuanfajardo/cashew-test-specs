# Cashew

[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) ![CocoaPods](https://img.shields.io/cocoapods/v/SwiftyJSON.svg) ![Platform](https://img.shields.io/badge/platforms-iOS%208.0+-333333.svg)

Carthage makes it easy to implement secure messaging into your app.

1. [Requirements](#requirements)
2. [Integration](#integration)
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

### Xcode
* Open project or workspace file after integrating SDK
* In Info.plist file add below properties
  * AMQP_MESSAGE_QUEUE_HOSTNAME
  * AMQP_REQUEST_QUEUE_HOSTNAME
  * AMQP_RESPONSE_QUEUE_HOSTNAME
  * CN_BASE_API_URL
  * HTTP_ATTACHMENT_SERVER_URL
  * HTTP_DIGEST_REALM

Fill the values for the Keys with appropriate URL's along with port numbers.

* Please include below lines of code in App delegate file in the method 

```objc
- (BOOL)application:(UIApplication *)application 
   didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
...
NSString * clientId = <<Please contact Loment Support for the values>>;
NSString * partnerId = <<Please contact Loment Support for the values>>;
[CNMUserServices registerWithSDK:clientId partnerId:partnerId];
...
}
```

* One above steps are done then you are authorized to use framework and its related functionality.
* In case of any Queries or support please contact Loment team for support support@loment.net 
