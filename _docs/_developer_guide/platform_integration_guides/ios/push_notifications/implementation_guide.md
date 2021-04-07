---
nav_title: Implementation Guide
platform: iOS
page_order: 7
description: "This implementation guide covers how to leverage push notification content extensions to get the most out of your push messages. Also included are three use cases built by our team, accompanying code snippets, and guidance on logging analytics."
---

# Push Notification Implementation Guide

> This implementation guide covers how to leverage push notification content extensions to get the most out of your push messages. Included are three use cases built by our team, accompanying code snippets, and guidance on logging analytics. Visit our Braze Demo Repository [here](https://github.com/braze-inc/braze-growth-shares-ios-demo-app)! Please note that this implementation guide is centered around a Swift implementation, but Objective-C snippets are provided for those interested.

## Notification Content Extensions

![Push Content Extension][1]{: style="max-width:65%;border:0;margin-top:10px"}

Push notifications while seemingly standard across different platforms, offer immense customization options past what is normally implemented in the default UI. When a push notification is expanded, content notification extensions enable a custom view of the expanded push notification. 

Push notifications can be expanded in three different ways: <br>- A long press on the push banner<br>- Swiping down on the push banner<br>- Swiping the banner to the right and selecting "View" 

These custom views offer smart ways to engage customers allowing you to display many distinct types of content including interactive notifications, notifications populated with user data, and even push messages that can capture information like phone numbers and email. While implementing push in this way may be unfamiliar to some, one of our well-known features at Braze, [Push Stories]({{site.baseurl}}/user_guide/message_building_by_channel/push/advanced_push_options/push_stories/), are a prime example of what a custom view for notification content extension can look like!

## Use Case and Implementation Walkthrough

There are three push notification content extension types provided. Each type has a concept walkthrough, potential use cases, and a look into how push notification variables may look and be used in the Braze dashboard:
- [Interactive Push Notification](#interactive-push-notification)
- [Personalized Push Notifications](#personalized-push-notifications)
- [Information Capture Push Notifications](#information-capture-push-notification)

### Interactive Push Notification

Push notifications can respond to user actions inside a content extension. As of iOS 12, content extensions now have the option of being interactive! This interactivity offers many possibilities to get your users engaged in your push notification. This can be achieved by having your users expand the push notification they receive.

![Push Content Extension][12]{: style="border:0"}

#### Dashboard Configuration

To set up a custom view in the dashboard you must toggle on notification buttons and enter your custom category. The pre-registered custom iOS category you provide is then checked against the code in the .plist of your Notification Content Extension Target.

{% alert tip %}
Since pushes with content extensions aren't always apparent, it is recommended to include a call to action to push your users to expand their push notifications.
{% endalert %}

In the content extension's _Info.plist_ file, the attribute `UNNotificationExtensionCategory` is set as a string "match_game". The value given here must match what is set in the Braze dashboard. 

![Interactive Push Dashboard Example][3]{: style="float:right;max-width:45%;"}
Lastly, you must also enable user interactions by toggling the `UNNotificationExtensionUserInteractionEnabled` attribute. After this, your touch is enabled in the .plist of your Notification Content Extension Target. 

![Interactive Push Dashboard Example][14]{: style="max-width:50%;"}

#### Other Use Cases
Push content extensions are an exciting option for companies to introduce interactivity in their promotions and applications. Some examples include a game for users to play, a spin-to-win wheel for discounts, or a "like" button to save a listing or song.

##### Ready to log analytics? 
Visit the [following section](#logging-analytics) to get a better understanding of how the flow of data should look.

### Personalized Push Notifications
![Personalized Push Dashboard Example][6]{: style="float:right;max-width:40%;margin-left:15px;border:0"}

Push notifications can display user-specific information inside a content extension. The example to the right shows a push notification after a user has completed a specific task (Braze LAB course) and is now encouraged to expand this notification to check their progress. The information provided here is detailed and user-specific and can be fired off as a session is completed or specific user action is taken.

#### Dashboard Configuration

To set up a personalized push in the dashboard, you must register the specific category you would like to be displayed, and then within the key-value pairs using standard Liquid, set the appropriate user attributes you would like the message to show. These views can be personalized based on specific user attributes of a specific user profile.

![Personalized Push Dashboard Example][5]{: style="max-width:60%;"}

#### Handling Key-Value Pairs

The method below, `didReceive` is called when the content extension has received a notification. The key-value pairs provided in the dashboard are represented in the code through the use of a `userInfo` dictionary.

__Parsing Key-Value Pairs from Push Notifications__<br>

{% tabs %}
{% tab Swift %}
``` swift 
func didReceive(_ notification: UNNotification) {
  let userInfo = notification.request.content.userInfo
     
  guard let value = userInfo["YOUR-KEY-VALUE-PAIR"] as? String,
        let otherValue = userInfo["YOUR-OTHER-KEY-VALUE-PAIR"] as? String,
  else { fatalError("Key-Value Pairs are incorrect.")}
 
  ...
}
```
{% endtab %}
{% tab Objective-C %}
```objc
- (void)didReceiveNotification:(nonnull UNNotification *)notification {
  NSDictionary *userInfo = notification.request.content.userInfo;
   
  if ([userInfo objectForKey:@"YOUR-KEY-VALUE-PAIR"] && [userInfo objectForKey:@"YOUR-OTHER-KEY-VALUE-PAIR"]) {
 
  ...
 
  } else {
    [NSException raise:NSGenericException format:@"Key-Value Pairs are incorrect"];
  }
}
```
{% endtab %}
{% endtabs %}

#### Other Use Cases

The ideas for progress-based and user-focused push content extensions are endless, some examples include adding the option to share your progress across different platforms, expressing achievements unlocked, punch cards, or even onboarding checklists. 

##### Ready to log analytics?
Visit the [following section](#logging-analytics) to get a better understanding of how the flow of data should look.

### Information Capture Push Notification

Push notifications can capture user information inside a content extension, allowing you to push the limits of what is possible with a push. Examining the flow shown below, the view is able to respond to state changes. Those state change components are represented in each image. 

1. User receives a push, and push is opened and prompts the user for information. 
2. Information is given, if valid, the register button is shown.
3. Confirmation view is displayed.
4. Push is dismissed. 

![Information Capture Push Dashboard Example][8]{: style="border:0;"}

Note that the information requested here can be a wide range of things such as SMS number capture, it doesn't have to be email-specific.

#### Dashboard Configuration

To set up an information capture capable push in the dashboard, you must register and set your custom category, and provide the key-value pairs that are needed. As seen in the example, you may also include an image in your push. To do this, you must set the notification style to Rich Notification and include a rich push image as you normally would. 

![Information Capture Push Dashboard Example][9]

#### Handling Button Actions

Each action button is uniquely identified. The code checks if your response identifier is equal to the `actionIndentifier`, and if so, knows that the user clicked the action button. From here, on your backend, you should save this event and attribute, and lastly, dismiss the notification. 

__Handling Push Notification Action Button Responses__<br>
Push notification action buttons are uniquely identified to handle responses from button presses accordingly.

{% tabs %}
{% tab Swift %}
``` swift 
func didReceive(_ response: UNNotificationResponse, completionHandler completion: @escaping (UNNotificationContentExtensionResponseOption) -> Void) {
  if response.actionIdentifier == "YOUR-REGISTER-IDENTIFIER" {
    completion(.dismiss)
  } else {
    completion(.doNotDismiss)
  }
}
```
{% endtab %}
{% tab Objective-C %}
```objc
- (void)didReceiveNotificationResponse:(UNNotificationResponse *)response completionHandler:(void (^)(UNNotificationContentExtensionResponseOption))completion {
  if ([response.actionIdentifier isEqualToString:@"YOUR-REGISTER-IDENTIFIER"]) {
    completion(UNNotificationContentExtensionResponseOptionDismiss);
  } else {
    completion(UNNotificationContentExtensionResponseOptionDoNotDismiss);
  }
}
```
{% endtab %}
{% endtabs %}

##### Dismissing Pushes

Push notifications can be automatically dismissed from an action button press. There exist three pre-built push dismissal options that are recommended:

1. completion(.dismiss) - Dismisses the notification
2. completion(.doNotDismiss) - Notification stays open
3. completion(.dismissAndForward) - Push dismisses and the user gets forwarded into the application.

#### Other Use Cases

Requesting user input through push notifications is an exciting opportunity that many companies do not take advantage of. In these push messages, you can not only request basic information like name, email, or number, but you could also prompt users to complete a user profile if unfinished, or even to submit feedback. 

##### Ready to log analytics?
Visit the section below to get a better understanding of how the flow of data should look. 

## Logging Analytics

### Logging with the Braze API - Recommended

Logging analytics can only be done in real-time with the help of the customer's server hitting Braze's API [users/track]({{site.baseurl}}/api/endpoints/user_data/post_user_track/) endpoint. To log analytics, send down the `braze_id` value in the key-value pairs field (as seen in the screenshot above) to identify which user profile to update.

### Logging Manually 

Logging manually will require you to create, save, and retrieve analytics, this requires some custom developer work on your end. The code snippets shown below will help address this. 

It's also important to note that analytics are not sent to Braze until the mobile application is subsequently launched. This means that, depending on your dismissal settings, there often exists an indeterminate period of time between when a push notification is dismissed and the mobile app is launched and the analytics are retrieved. While this time buffer may not affect all use cases, users should consider the impact and if necessary, adjust their user journey to include opening the application to address this concern. 

![Push Logging][13]

#### Code Snippets

The following code snippets are a helpful reference on how to save and send custom events, custom attributes, and user attributes. This guide will be speaking in terms of UserDefaults, but the code representation will be in the form of a helper file  `RemoteStorage`. There also exists an additional helper file `UserAttributes`that is used when sending and saving user attributes. Both helper files can be found below.

{% tabs local %}
{% tab Custom Events %}

##### Saving Custom Events

To save custom events you must create the analytics from scratch. This is done by creating a dictionary, populating it with metadata, and saving the data through the use of a helper file.

1. Initialize a dictionary with event metadata
2. Initialize userDefaults to retrieve and store the event data
3. If there is an existing array, append new data to the existing array and save
4. If there is not an existing array, save the new array to userDefaults

{% subtabs local %}
{% subtab Swift %}
``` swift 
func saveCustomEvent(with properties: [String: Any]? = nil) {
  // 1
  let customEventDictionary = Dictionary(eventName: "YOUR-EVENT-NAME", properties: properties)
  
  // 2
  let remoteStorage = RemoteStorage(storageType: .suite)
  
  // 3   
  if var pendingEvents = remoteStorage.retrieve(forKey: .pendingCustomEvents) as? [[String: Any]] {
    pendingEvents.append(contentsOf: [customEventDictionary])
    remoteStorage.store(pendingEvents, forKey: .pendingCustomEvents)
  } else {
  // 4
    remoteStorage.store([customEventDictionary], forKey: .pendingCustomEvents)
  }
}
```
{% endsubtab %}
{% subtab Objective-C %}
```objc
- (void)saveCustomEvent:(NSDictionary<NSString *, id> *)properties {
  // 1 
  NSDictionary<NSString *, id> *customEventDictionary = [[NSDictionary alloc] initWithEventName:@"YOUR-EVENT-NAME" properties:properties];
  
  // 2
  RemoteStorage *remoteStorage = [[RemoteStorage alloc] initWithStorageType:StorageTypeSuite];
  NSMutableArray *pendingEvents = [[remoteStorage retrieveForKey:RemoteStorageKeyPendingCustomEvents] mutableCopy];
  
  // 3 
  if (pendingEvents) {
    [pendingEvents addObject:customEventDictionary];
    [remoteStorage store:pendingEvents forKey:RemoteStorageKeyPendingCustomAttributes];
  } else {
  // 4
    [remoteStorage store:@[ customEventDictionary ] forKey:RemoteStorageKeyPendingCustomAttributes];
  }
}
```
{% endsubtab %}
{% endsubtabs %}

##### Sending Custom Events to Braze

After the SDK is initialized is the best time to log any saved analytics from a notification content extension. This can be done by, looping through any pending events, checking for the "Event Name" key, setting the appropriate values in Braze, and then clearing the storage for the next time this function is needed.

1. Loop through the array of pending events
2. Loop through each key-value pair in the pending event dictionary
3. Explicitly checking key for “Event Name” to set the value accordingly
4. Every other key-value will be added to the properties dictionary
5. Log individual custom event 
6. Remove all pending events from storage

{% subtabs local %}
{% subtab Swift %}
``` swift 
func logPendingCustomEventsIfNecessary() {
  let remoteStorage = RemoteStorage(storageType: .suite)
  guard let pendingEvents = remoteStorage.retrieve(forKey: .pendingCustomEvents) as? [[String: Any]] else { return }
  
  // 1    
  for event in pendingEvents {
    var eventName: String?
    var properties: [AnyHashable: Any] = [:]
    
  // 2
    for (key, value) in event {
      if key == PushNotificationKey.eventName.rawValue {
  // 3      
        if let eventNameValue = value as? String {
          eventName = eventNameValue
        } else {
          print("Invalid type for event_name key")
        }
      } else {
  // 4 
        properties[key] = value
      }
    }
  // 5    
    if let eventName = eventName {
      logCustomEvent(eventName, withProperties: properties)
    }
  }

  // 6    
  remoteStorage.removeObject(forKey: .pendingCustomEvents)
}
```
{% endsubtab %}
{% subtab Objective-C %}
```objc
- (void)logPendingEventsIfNecessary {
  RemoteStorage *remoteStorage = [[RemoteStorage alloc] initWithStorageType:StorageTypeSuite];
  NSArray *pendingEvents = [remoteStorage retrieveForKey:RemoteStorageKeyPendingCustomEvents];
  
  // 1 
  for (NSDictionary<NSString *, id> *event in pendingEvents) {
    NSString *eventName = nil;
    NSMutableDictionary *properties = [NSMutableDictionary dictionary];
    
  // 2 
    for (NSString* key in event) {
      if ([key isEqualToString:@"event_name"]) {
  // 3       
        if ([[event objectForKey:key] isKindOfClass:[NSString class]]) {
          eventName = [event objectForKey:key];
        } else {
          NSLog(@"Invalid type for event_name key");
        }
      } else {
  // 4 
        [properties setValue:[event objectForKey:key] forKey:key];
      }
    }
  // 5  
    if (eventName != nil) {
      [[Appboy sharednstance] logCustomEvent:eventName withProperties:properties];
    }
  }

  // 6  
  [remoteStorage removeObjectForKey:RemoteStorageKeyPendingCustomEvents];
}
```
{% endsubtab %}
{% endsubtabs %}
{% endtab %}
{% tab Custom Attributes %}

##### Saving Custom Attributes

To save custom attributes you must create the analytics from scratch. This is done by creating a dictionary, populating it with metadata, and saving the data through the use of a helper file.

1. Initialize a dictionary with attribute metadata
2. Initialize userDefaults to retrieve and store the attribute data
3. If there is an existing array, append new data to the existing array and save
4. If there is not an existing array, save the new array to userDefaults

{% subtabs local %}
{% subtab Swift %}
``` swift 
func saveCustomAttribute() {
  // 1 
  let customAttributeDictionary: [String: Any] = ["YOUR-CUSTOM-ATTRIBUTE-KEY": "YOUR-CUSTOM-ATTRIBUTE-VALUE"]
  
  // 2 
  let remoteStorage = RemoteStorage(storageType: .suite)
  
  // 3 
  if var pendingAttributes = remoteStorage.retrieve(forKey: .pendingCustomAttributes) as? [[String: Any]] {
    pendingAttributes.append(contentsOf: [customAttributeDictionary])
    remoteStorage.store(pendingAttributes, forKey: .pendingCustomAttributes)
  } else {
  // 4 
    remoteStorage.store([customAttributeDictionary], forKey: .pendingCustomAttributes)
  }
}
```
{% endsubtab %}
{% subtab Objective-C %}
``` objc
- (void)saveCustomAttribute {
  // 1 
  NSDictionary<NSString *, id> *customAttributeDictionary = @{ @"YOUR-CUSTOM-ATTRIBUTE-KEY": @"YOUR-CUSTOM-ATTRIBUTE-VALUE" };
  
  // 2  
  RemoteStorage *remoteStorage = [[RemoteStorage alloc] initWithStorageType:StorageTypeSuite];
  NSMutableArray *pendingAttributes = [[remoteStorage retrieveForKey:RemoteStorageKeyPendingCustomAttributes] mutableCopy];
  
  // 3
  if (pendingAttributes) {
    [pendingAttributes addObject:customAttributeDictionary];
    [remoteStorage store:pendingAttributes forKey:RemoteStorageKeyPendingCustomAttributes];
  } else {
  // 4 
    [remoteStorage store:@[ customAttributeDictionary ] forKey:RemoteStorageKeyPendingCustomAttributes];
  }
}
```
{% endsubtab %}
{% endsubtabs %}

##### Sending Custom Attributes to Braze

After the SDK is initialized is the best time to log any saved analytics from a notification content extension. This can be done by looping through the pending attributes, setting the appropriate custom attribute in Braze, and then clearing the storage for the next time this function is needed.

1. Loop through the array of pending attributes
2. Loop through each key-value pair in the pending attributes dictionary
3. Log individual custom attribute with corresponding key and value
4. Remove all pending attributes from storage

{% subtabs local %}
{% subtab Swift %}
``` swift 
func logPendingCustomAttributesIfNecessary() {
  let remoteStorage = RemoteStorage(storageType: .suite)
  guard let pendingAttributes = remoteStorage.retrieve(forKey: .pendingCustomAttributes) as? [[String: Any]] else { return }
     
  // 1
  pendingAttributes.forEach { setCustomAttributesWith(keysAndValues: $0) }
  
  // 4 
  remoteStorage.removeObject(forKey: .pendingCustomAttributes)
}
   
func setCustomAttributesWith(keysAndValues: [String: Any]) {
  // 2 
  for (key, value) in keysAndValues {
  // 3
    if let value = value as? [String] {
      setCustomAttributeArrayWithKey(key, andValue: value)
    } else {
      setCustomAttributeWithKey(key, andValue: value)
    }
  }
}
```
{% endsubtab %}
{% subtab Objective-C %}
```objc
- (void)logPendingCustomAttributesIfNecessary {
  RemoteStorage *remoteStorage = [[RemoteStorage alloc] initWithStorageType:StorageTypeSuite];
  NSArray *pendingAttributes = [remoteStorage retrieveForKey:RemoteStorageKeyPendingCustomAttributes];
   
  // 1
  for (NSDictionary<NSString*, id> *attribute in pendingAttributes) {
    [self setCustomAttributeWith:attribute];
  }

  // 4 
  [remoteStorage removeObjectForKey:RemoteStorageKeyPendingCustomAttributes];
}
 
- (void)setCustomAttributeWith:(NSDictionary<NSString *, id> *)keysAndValues {
  // 2
  for (NSString *key in keysAndValues) {
  // 3 
    [self setCustomAttributeWith:key andValue:[keysAndValues objectForKey:key]];
  }
}
```
{% endsubtab %}
{% endsubtabs %}
{% endtab %}
{% tab User Attributes %}

##### Saving User Attributes

When saving user attributes, it is recommended to create a custom object to decipher what type of attribute is being updated (email, first_name, phone_number, etc.). The object should be compatible with being stored/retrieved from UserDefaults. See the `UserAttribute` helper file for one example of how to accomplish this.

1. Initialize an encoded UserAttribute object with the corresponding type
2. Initialize userDefaults to retrieve and store the event data
3. If there is an existing array, append new data to the existing array and save
4. If there is not an existing array, save the new array to userDefaults

{% subtabs local %}
{% subtab Swift %}
``` swift 
func saveUserAttribute() {
  // 1 
  guard let data = try? PropertyListEncoder().encode(UserAttribute.userAttributeType("USER-ATTRIBUTE-VALUE")) else { return }
  
  // 2       
  let remoteStorage = RemoteStorage(storageType: .suite)
  
  // 3    
  if var pendingAttributes = remoteStorage.retrieve(forKey: .pendingUserAttributes) as? [Data] {
    pendingAttributes.append(contentsOf: [data])
    remoteStorage.store(pendingAttributes, forKey: .pendingUserAttributes)
  } else {
  // 4 
    remoteStorage.store([data], forKey: .pendingUserAttributes)
  }
}
```
{% endsubtab %}
{% subtab Objective-C %}
```objc
- (void)saveUserAttribute {
  // 1 
  UserAttribute *userAttribute = [[UserAttribute alloc] initWithUserField:@"USER-ATTRIBUTE-VALUE" attributeType:UserAttributeTypeEmail];
   
  NSError *error;
  NSData *data = [NSKeyedArchiver archivedDataWithRootObject:userAttribute requiringSecureCoding:YES error:&error];
  
  // 2  
  RemoteStorage *remoteStorage = [[RemoteStorage alloc] initWithStorageType:StorageTypeSuite];
  NSMutableArray *pendingAttributes = [[remoteStorage retrieveForKey:RemoteStorageKeyPendingUserAttributes] mutableCopy];
  
  // 3 
  if (pendingAttributes) {
    [pendingAttributes addObject:data];
    [remoteStorage store:pendingAttributes forKey:RemoteStorageKeyPendingUserAttributes];
  } else {
  // 4 
    [remoteStorage store:@[data] forKey:RemoteStorageKeyPendingUserAttributes];
  }
}
```
{% endsubtab %}
{% endsubtabs %}

##### Sending User Attributes to Braze

After the SDK is initialized is the best time to log any saved analytics from a notification content extension. This can be done by looping through the pending attributes, setting the appropriate custom attribute in Braze, and then clearing the storage for the next time this function is needed.

1. Loop through the array of pending attribute data
2. Initialize an encoded UserAttribute object from attribute data
3. Set specific user field based on the User Attribute type (email)
4. Remove all pending user attributes from storage

{% subtabs local %}
{% subtab Swift %}
``` swift 
func logPendingUserAttributesIfNecessary() {
  let remoteStorage = RemoteStorage(storageType: .suite)
  guard let pendingAttributes = remoteStorage.retrieve(forKey: .pendingUserAttributes) as? [Data] else { return }
  
  // 1    
  for attributeData in pendingAttributes {
  // 2 
    guard let userAttribute = try? PropertyListDecoder().decode(UserAttribute.self, from: attributeData) else { continue }
    
  // 3    
    switch userAttribute {
    case .email(let email):
      user?.email = email
    }
  }
  // 4   
  remoteStorage.removeObject(forKey: .pendingUserAttributes)
}
```
{% endsubtab %}
{% subtab Objective-C %}
```objc
- (void)logPendingUserAttributesIfNecessary {
  RemoteStorage *remoteStorage = [[RemoteStorage alloc] initWithStorageType:StorageTypeSuite];
  NSArray *pendingAttributes = [remoteStorage retrieveForKey:RemoteStorageKeyPendingUserAttributes];
  
  // 1  
  for (NSData *attributeData in pendingAttributes) {
    NSError *error;
  // 2 
    UserAttribute *userAttribute = [NSKeyedUnarchiver unarchivedObjectOfClass:[UserAttribute class] fromData:attributeData error:&error];
    
  // 3  
    if (userAttribute) {
      switch (userAttribute.attributeType) {
        case UserAttributeTypeEmail:
          [self user].email = userAttribute.userField;
          break;
      }
    }
  }
  // 4 
  [remoteStorage removeObjectForKey:RemoteStorageKeyPendingUserAttributes];
}
```
{% endsubtab %}
{% endsubtabs %}
{% endtab %}
{% tab Helper Files %}

##### Helper Files

{% details RemoteStorage Helper File %}
{% subtabs local %}
{% subtab Swift %}
```swift
enum RemoteStorageKey: String, CaseIterable {
   
  // MARK: - Notification Content Extension Analytics
  case pendingCustomEvents = "pending_custom_events"
  case pendingCustomAttributes = "pending_custom_attributes"
  case pendingUserAttributes = "pending_user_attributes"
}
 
enum RemoteStorageType {
  case standard
  case suite
}
 
class RemoteStorage: NSObject {
  private var storageType: RemoteStorageType = .standard
  private lazy var defaults: UserDefaults = {
    switch storageType {
    case .standard:
      return .standard
    case .suite:
      return UserDefaults(suiteName: "YOUR-APP=GROUP")!
    }
  }()
   
  init(storageType: RemoteStorageType = .standard) {
    self.storageType = storageType
  }
   
  func store(_ value: Any, forKey key: RemoteStorageKey) {
    defaults.set(value, forKey: key.rawValue)
  }
   
  func retrieve(forKey key: RemoteStorageKey) -> Any? {
    return defaults.object(forKey: key.rawValue)
  }
   
  func removeObject(forKey key: RemoteStorageKey) {
    defaults.removeObject(forKey: key.rawValue)
  }
   
  func resetStorageKeys() {
    for key in RemoteStorageKey.allCases {
      defaults.removeObject(forKey: key.rawValue)
    }
  }
}
```
{% endsubtab %}
{% subtab Objective-C %}
```objc
@interface RemoteStorage ()
 
@property (nonatomic) StorageType storageType;
@property (nonatomic, strong) NSUserDefaults *defaults;
 
@end
 
@implementation RemoteStorage
 
- (id)initWithStorageType:(StorageType)storageType {
  if (self = [super init]) {
    self.storageType = storageType;
  }
  return self;
}
 
- (void)store:(id)value forKey:(RemoteStorageKey)key {
  [[self defaults] setValue:value forKey:[self rawValueForKey:key]];
}
 
- (id)retrieveForKey:(RemoteStorageKey)key {
  return [[self defaults] objectForKey:[self rawValueForKey:key]];
}
 
- (void)removeObjectForKey:(RemoteStorageKey)key {
  [[self defaults] removeObjectForKey:[self rawValueForKey:key]];
}
 
- (void)resetStorageKeys {
  [[self defaults] removeObjectForKey:[self rawValueForKey:RemoteStorageKeyPendingCustomEvents]];
  [[self defaults] removeObjectForKey:[self rawValueForKey:RemoteStorageKeyPendingCustomAttributes]];
  [[self defaults] removeObjectForKey:[self rawValueForKey:RemoteStorageKeyPendingUserAttributes]];
}
 
- (NSUserDefaults *)defaults {
  if (!self.defaults) {
    switch (self.storageType) {
      case StorageTypeStandard:
        return [NSUserDefaults standardUserDefaults];
        break;
      case StorageTypeSuite:
        return [[NSUserDefaults alloc] initWithSuiteName:@"YOUR-SUITE-NAME"];
    }
  } else {
    return self.defaults;
  }
}
 
- (NSString*)rawValueForKey:(RemoteStorageKey)remoteStorageKey {
    switch(remoteStorageKey) {
    case RemoteStorageKeyPendingCustomEvents:
      return @"pending_custom_events";
    case RemoteStorageKeyPendingCustomAttributes:
      return @"pending_custom_attributes";
    case RemoteStorageKeyPendingUserAttributes:
      return @"pending_user_attributes";
    default:
      [NSException raise:NSGenericException format:@"Unexpected FormatType."];
  }
}
```
{% endsubtab %}
{% endsubtabs %}
{% enddetails %}

{% details UserAttribute Helper File %}
{% subtabs local %}
{% subtab Swift %}
```swift
enum UserAttribute: Hashable {
  case email(String?)
}
 
// MARK: - Codable
extension UserAttribute: Codable {
  private enum CodingKeys: String, CodingKey {
    case email
  }
   
  func encode(to encoder: Encoder) throws {
    var values = encoder.container(keyedBy: CodingKeys.self)
     
    switch self {
    case .email(let email):
      try values.encode(email, forKey: .email)
    }
  }
   
  init(from decoder: Decoder) throws {
    let values = try decoder.container(keyedBy: CodingKeys.self)
     
    let email = try values.decode(String.self, forKey: .email)
    self = .email(email)
  }
}
```
{% endsubtab %}
{% subtab Objective-C %}
```objc
@implementation UserAttribute
 
- (id)initWithUserField:(NSString *)userField attributeType:(UserAttributeType)attributeType {
  if (self = [super init]) {
    self.userField = userField;
    self.attributeType = attributeType;
  }
  return self;
}
 
- (void)encodeWithCoder:(NSCoder *)encoder {
  [encoder encodeObject:self.userField forKey:@"userField"];
  [encoder encodeInteger:self.attributeType forKey:@"attributeType"];
}
 
- (id)initWithCoder:(NSCoder *)decoder {
  if (self = [super init]) {
    self.userField = [decoder decodeObjectForKey:@"userField"];
     
    NSInteger attributeRawValue = [decoder decodeIntegerForKey:@"attributeType"];
    self.attributeType = (UserAttributeType) attributeRawValue;
  }
  return self;
}
 
@end
```
{% endsubtab %}
{% endsubtabs %}
{% enddetails %}
<br>
{% endtab %}
{% endtabs %}

[1]: {% image_buster /assets/img/push_implementation_guide/push1.png %}
[2]: {% image_buster /assets/img/push_implementation_guide/push2.png %}
[3]: {% image_buster /assets/img/push_implementation_guide/push3.png %}
[4]: {% image_buster /assets/img/push_implementation_guide/push4.png %}
[5]: {% image_buster /assets/img/push_implementation_guide/push5.png %}
[6]: {% image_buster /assets/img/push_implementation_guide/push6.png %}
[7]: {% image_buster /assets/img/push_implementation_guide/push7.png %}
[8]: {% image_buster /assets/img/push_implementation_guide/push8.png %}
[9]: {% image_buster /assets/img/push_implementation_guide/push9.png %}
[10]: {% image_buster /assets/img/push_implementation_guide/push10.png %}
[11]: {% image_buster /assets/img/push_implementation_guide/push11.png %}
[12]: {% image_buster /assets/img/push_implementation_guide/push12.png %}
[13]: {% image_buster /assets/img/push_implementation_guide/push13.png %}
[14]: {% image_buster /assets/img/push_implementation_guide/push14.png %}