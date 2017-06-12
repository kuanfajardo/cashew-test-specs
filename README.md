# Cashew

[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) ![CocoaPods](https://img.shields.io/cocoapods/v/SwiftyJSON.svg) ![Platform](https://img.shields.io/badge/platforms-iOS%208.0+-333333.svg)

Cashew makes it easy to implement secure messaging into your app.

* [Requirements](#requirements)
* [Integration](#integration)
   - [Installation](#installation)
   - [Setting up Xcode](#setting-up-xcode)
* [Documentation](#documentation)
* [Usage](#usage)
   - [Registration](#registration)
   - [Login and Account Creation](#login-and-account-creation)
   - [Messaging](#messaging)
   - [Groups](#groups)
* [License](#license)

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

### Registration

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

### Login and Account Creation

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

### Messaging

Now that we have our user created and logged in and the user has at least one cashew account associated with it, the next step is to start the message services. All Cashew messages are delivered via AMQP message queues, messages cannot be sent or received until the message queues are started. To start the message services, call ```startServiceForCashewAccount:```


```objc
//Start messaging service
[CNMMessagingServices
	startServiceForCashewAccount:self.selectedCashewAccount	
        user:self.user 
        withCompletion:^(NSError*error)
        {
	   if(error)
	   { 
		//Handle message service failed to start 
	   }
        }
	serviceInteruption:^(NSError*error) 
        {
	    //Handle message queue failure
        }];
```

Now that the message services are started, the next step is to create a contact to have a conversation with.

```objc
[CNMMessagingServices
	addContact:textField.text
        withCompletion:^(CNMContact *contact,NSError*error)
        {
	   if(!error)
	   { 
		// Handle success 
	   }
	   else
	   {
		// Handle failure
	   }
         }
```

All cashew messages occur in the context of a conversation. The conversation happens with a specific recipient, so we pass in a contact object to the ```createNewConversationWithOtherCashewContactcall:```

```objc

[CNMMessagingServices
	createNewConversationWithOtherCashewContact:contact
		withCompletion:^(CNMConversation*convo,NSError*error)
        {
	   if(!error)
	   { 
		// Handle success 
	   }
	   else
	   {
		// Handle failure
	   } 
        }];

```

Now that the conversation has been established, we can send messages to the contact:

```objc

[CNMMessagingServices
	sendNewMessage:messageToSend
	withAttachmentFilename:attachmentFileName
	withAttachmentData:UIImagePNGRepresentation(self.attachmentImageView.image) 	
        forConversation:self.conversation
        withType:msgType
	withCompletion:^(NSError*error)
        {
	   if(!error)
	   { 
		// Handle success 
	   }
	   else
	   {
		// Handle failure
	   }
        }];

```

Below call will provide the unread count for each conversation

```objc
[CNMMessagingServices numberOfUnreadMessagesOnConversation:convo 
                      error:&error];
```


To get the list of conversations we need to do below steps

Steps: 
1. Take a view controller class which should import messaging SDK.

2. Class should inherit ```CNMContentDeliveryControllerDelegate```, ```CNMMessagingServicesListener```.

3. Declare a property to handle the content and to show it in table view

```objc
@property (nonatomic, strong) CNMContentDeliveryController * conversationDelivery;
@property (weak, nonatomic) IBOutlet UITableView *tableView;
```

4. Implement below function to get the list of conversations

```objc
- (void)getAllConversations
{
    NSError * e;
    CNMContentDeliveryController * delivery = [CNMMessagingServices allConversationsDeliveryController:&e];

    if (!e) {
        self.conversationDelivery = delivery;
        self.conversationDelivery.delegate = self;
        [self.conversationDelivery fetchData:&e];
        [self.tableView reloadData];
    }

    else {
        [[[UIAlertView alloc] initWithTitle:@"Warning" message:@"Failed to fetch conversations. You may not be actively connected to server for various reasons. Ensure you have an active internet conntection, and your credentials are correct" delegate:nil cancelButtonTitle:nil otherButtonTitles:@"ok", nil] show];
    }
}
```

5. Call the above method in ```viewWillAppear```.

```objc
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];

    [self didStartServices];//Listeners for CNMMessagingServicesListener
}

- (void)didStartServices
{
    [self getAllConversations];
}
```

6. Make sure to set conversationDelivery to nill on ```viewDidDisappear```.

```objc
- (void)viewDidDisappear:(BOOL)animated
{
    [super viewDidDisappear:animated];

    [self didStopServices]; // Listeners forCNMMessagingServicesListener
}

- (void)didStopServices
{
    self.conversationDelivery = nil;
}

```

7. Delegate methods for ```CNMContentDeliveryControllerDelegate``` should be implemented.

```objc
- (void)controllerWillChangeContent:(CNMContentDeliveryController *)controller
{
    [self.tableView beginUpdates];
}

- (void)controller:(CNMContentDeliveryController *)controller didChangeObject:(id)anObject atIndexPath:(NSIndexPath *)indexPath forChangeType:(CNMFetchResultsChangeType)type newIndexPath:(NSIndexPath *)newIndexPath
{
    UITableView * tableView = self.tableView;

    switch(type) {
        case CNMFetchResultsChangeInsert:
            [tableView insertRowsAtIndexPaths:@[newIndexPath] withRowAnimation:UITableViewRowAnimationFade];
            break;

        case CNMFetchResultsChangeDelete:
            [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationFade];
            break;

        case CNMFetchResultsChangeUpdate:
            [self configureCell:(ConversationTableViewCell *)[tableView cellForRowAtIndexPath:indexPath] atIndexPath:indexPath];
            break;

        case CNMFetchResultsChangeMove:
            [self configureCell:(ConversationTableViewCell *)[tableView cellForRowAtIndexPath:indexPath] atIndexPath:newIndexPath];
            [tableView moveRowAtIndexPath:indexPath toIndexPath:newIndexPath];
            break;
    }
}

- (void)controllerDidChangeContent:(CNMContentDeliveryController *)controller
{
    [self.tableView endUpdates];
}

- (void)configureCell:(ConversationTableViewCell *)cell atIndexPath:(NSIndexPath *)path
{
    // getting each conversation object
    CNMConversation *convo = [self.conversationDelivery objectAtIndexPath:path];

    // Set Participant name
    cell.participantsLabel.text = convo.conversationName;

    // Setting Last message Preview Label
    NSString * messagePlainText = convo.latestMessage.content;
    cell.lastMessagePreviewLabel.text = [messagePlainText substringToIndex:MIN(100, messagePlainText.length)];

    // Get the unread count.

    NSError * error;
    NSUInteger count = [CNMMessagingServices numberOfUnreadMessagesOnConversation:convo error:&error];
    NSAssert(!error, @"wat");

    // set it on the cell.
}
```


8. Implement ```UITableViewDataSource``` methods.

```objc
-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return self.conversationDelivery.numberOfObjects;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *simpleTableIdentifier = @"ConversationTableViewCell";

    ConversationTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:simpleTableIdentifier];

    if (!cell) {
        cell = [[ConversationTableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:simpleTableIdentifier];
    }

    // Set cells message body
    [self configureCell:cell atIndexPath:indexPath];

    return cell;
}
```

That’s it!

### Groups

The first step is to create a group with a name and at least one participant and the person who creates group will be the Main Admin (Owner) of that group:

```objc
[CNMMessagingServices createNewGroupConversation:groupName withContacts:groupMembers withCompletion:^(CNMConversation *conversation, NSError *error)
{
     
        if (!error)
        {
            //Handle success
        }
        else
        {
            //Handle failure
        }
      
}];
```

Now that the group is created we can change the Name of the group using below group call:

```objc
[CNMMessagingServices updateConversation:self.conversation newName:newGroupName 
    withCompletion:^(CNMConversation *conversation, NSError *error)
{
    
        if (!error)
        {
            //Handle success
        }
        else
        {
            //Handle failure
        }

}];
```

Now that the group is created we can change the Owner of the group using below group call:

```objc
[CNMMessagingServices updateConversation:self.conversation newOwner:self.selectedcontacts withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];
```


Now that the group is created we can add members to the group using below group call:

```objc
[CNMMessagingServices updateConversation:self.conversation addMembers:groupMembers withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];
```

Now that the group is created and we added members to the group if we want to remove members from Group we can do that using below call:

```objc
[CNMMessagingServices updateConversation:self.conversation removeMembers:self.selectedcontacts withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];
```

Now that the group is created and we added members to make some one as Admin on the group then we can do that using below call. This is valid operation if it is performed by group admin:

```objc
[CNMMessagingServices updateConversation:self.conversation addAdmins:self.selectedcontacts withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];
```

Now that the group is created and we added members and made some of the members as Admins and we want to remove them from Admin list then we can do that using below call. This is Valid operation if it is performed by Main admin of group:

```objc
[CNMMessagingServices updateConversation:self.conversation removeAdmins:self.selectedcontacts withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];
```

When a group is created it is created by default with option that Any member of the group can add new members to the group and if we don't want to happen then we can restrict that to only Main admin and Admins then we can do that using below call and this is a valid only if Main admin of the group performs this operation:

```objc
[CNMMessagingServices updateConversationFeaturesRemoved:self.conversation 
    withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];

```

Now to give permission to all the members of group to add participants. this can be done using below call :

```objc
[CNMMessagingServices updateConversationFeaturesAdded:self.conversation 
    withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];
```

You can leave Group using using below call. This is valid operation if the you are only a member not Main admin (or) Admin :

```objc
[CNMMessagingServices leaveConversation:self.conversation 
    withCompletion:^(CNMConversation *conversation, NSError *error)
{

    if (!error)
    {
        //Handle success
    }
    else
    {
        //Handle failure
    }

}];

```

## License
Copyright 2017 Loment, Inc. All rights reserved.
