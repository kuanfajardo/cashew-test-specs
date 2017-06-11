# Cashew

[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) ![CocoaPods](https://img.shields.io/cocoapods/v/SwiftyJSON.svg) ![Platform](https://img.shields.io/badge/platforms-iOS%208.0+-333333.svg)

Cashew makes it easy to implement secure messaging into your app.

1. [Requirements](#requirements)
2. [Integration](#integration)
   - [Installation](#installation)
   - [Setting up Xcode](#setting-up-xcode)
3. [Documentation](#documentation)
4. [Usage](#usage)
   - [Registration](#registration)
   - [Login and Account Creation](#login-and-account-creation)
   - [Messaging](#messaging)
   - [Groups](#groups)

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

## Documentation

You can find the full documentation of Cashew [here](https://kuanfajardo.github.io/cashew-test-specs/).

## Usage

#### Registration

The first step to using the Cashew SDK is to register a new user. This is accomplished by calling ```registerUserWithName:``` and passing in valid name, password, email, phone number and country code:

```objc
[CNMUserServices 
	registerUserWithName: self.nameTextField.text 	
        withPassword:self.passwordTextField.text
	withEmail:self.emailTextField.text 		
        withPhone:self.phoneNumberTextField.text 	
        withIsoCode:self.countryCodeTextField.text 	
        withCompletion:^(CNMUser*user,NSError*error)
        {
	   dispatch_async(dispatch_get_main_queue(),^{ 
		if(!error){
			//Handle success
		} else {
			//Handle failure
		}

		[self dismiss]; 
	});
}];
```

#### Login and Account Creation

Once the new account has been created the next step is to login to the Cashew server. This is accomplished by using the ```CNMUserServices``` class:

```objc
[CNMUserServices
	authenticateUser:self.usernameTextField.text
        andPassword:self.passwordTextField.text
	withCompletion:^(CNMUser*user,NSError*error)
        {
	   dispatch_async(dispatch_get_main_queue(),^ 
	   {
		if(!error)
		{
			//Handle success
		}
		else
		{
		}
	}); 
}];
```

At this point we need to understand a tiny bit about the Loment Cashew architecture. The Cashew architecture allows a single user to have multiple independent accounts. In this way a single user can have a work account, a friends account, a family account, etc. But to use the system a user must have <i>at least one</i> account. Here we are creating a single account for a user. This only needs to be called once, not everytime we login. But it does need to be called after we login because we need to know what user to add the account to:

```objc
[CNMUserServices 
	addCashewUsername:name 
	forUser:self.user 
	withCompletion:^(CNMUser *user, NSError *error)
        {
	  dispatch_async(dispatch_get_main_queue(), ^{ 
		if (!error)
		{
		} else {
			// Handle Failure
		} 
	});
}];
```

#### Messaging

#### Groups


