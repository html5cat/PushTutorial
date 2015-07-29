Using push notifications is a great way to add real-time messaging to your application. It allows you to stay in touch with your users and makes it possible for your users to communicate with each other. This tutorial will guide you through all the necessary steps required to add this feature to your Parse application.

We will begin on the Apple Developer website to create an SSL certificate associated to an App ID and a provisioning profile. Next we'll see how to configure a Parse app on the Parse website, and finally, we'll take a look at creating a push-enabled iOS application and sending notifications to users.

Before you begin, remember that push notifications are not available in the iOS Simulator. You will need an iOS device, as well as an Apple Developer license to complete this tutorial.

## Troubleshooting

If you're here because you are having trouble setting up push notifications, check out our [troubleshooting guide](https://parse.com/docs/android/guide#push-notifications-troubleshooting).

# 1. Creating the SSL certificate

The first step is to create an App ID and the associated SSL certificate on the Apple Developer website. This certificate will allow the Parse server to send push notifications to the application identified by the App ID.

## 1.1. Generating a Certificate Request

To begin, we'll need a certificate signing request file. This will be used to authenticate the creation of the SSL certificate.

1. Launch the Keychain Access application on your Mac.

2. Select the menu item Keychain Access > Certificate Assistant > Request a Certificate From a Certificate Authority…

3. Enter your email address and name.

4. Select "Saved to disk" to download the .certSigningRequest file to your desktop.

<center><img src="https://www.evernote.com/shard/s6/sh/62a8478d-8136-4f3f-8484-5f4939ee6763/c4e75c123c1ca3a999f9e6ef74424874/deep/0/Screenshot%204/23/13%2010:09%20AM.jpg" alt="Certificate Signing Request"/></center>

## 1.2. Creating an App ID
Every iOS application installed on your developer device needs an App ID. As a convention, these are represented by reversed addresses (ex. com.example.MyParsePushApp). For this push app, you can use an App ID you've already created, but make sure it does not contain a wildcard character ("*"). The following instructions cover the creation of a new App ID.

1. Navigate to the [Apple Developer Member Center](https://developer.apple.com/membercenter/index.action) website, and select [Certificates, Identifiers & Profiles](https://developer.apple.com/account/overview.action).

2. Select <a href="https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action">Identifiers</a> from the iOS Apps section.

3. You will see a list of your iOS App IDs. Select the + button to register a new App Id.

<center><img src="https://www.evernote.com/shard/s6/sh/6be0efeb-d7e8-4fd7-acfc-b79badde1b17/db5a741bac5cd8441c59a93e212930ff/deep/0/Screenshot%204/23/13%2010:15%20AM.jpg" alt="Register new iOS App ID"/></center>

4. Enter a name for your new App ID, then make sure to select the checkbox next to Push Notifications under App Services.

<center><img src="https://www.evernote.com/shard/s6/sh/e5a60f5f-02e2-4165-b774-05a65381a54e/d2025ba4854a0c18a03872f7b22c279c/deep/0/Screenshot%204/23/13%2010:19%20AM.jpg" alt="Enable Push Notifications under App Services"/></center>

5. Choose an App ID Prefix. The default selection should be correct in most cases.

6. Under App ID Suffix, select Explicit App ID. Enter your iOS app's Bundle ID. This string should match the Bundle Identifier in your iOS app's Info.plist.

<center><img src="https://www.evernote.com/shard/s6/sh/088a2e71-8596-431e-873c-b2f262934b86/0a24d02ca5503ee196e452cb75909de0/deep/0/Screenshot%204/23/13%2010:20%20AM.jpg" alt="Explicit App ID"/></center>

7. Select "Continue" and make sure that all the values were entered correctly. Push Notifications should be enabled, and the Identifier field should match your app's Bundle Identifier (plus App ID Prefix). Select "Submit" to finalize the registration of your new App ID.

## 1.3. Configuring your App ID for Development Push Notifications

Now that you've created an App ID (or chosen an existing Explicit App ID), it's time to configure the App ID for Push Notifications.

1. Select your newly created App ID from the list of iOS App IDs, then select "Settings".

<center><img src="https://www.evernote.com/shard/s6/sh/e1de2b43-71c3-4f96-ba72-c109afdadc2b/15ba546dba48b5c84c762d86af3935a0/deep/0/Screenshot%204/23/13%2010:28%20AM.jpg" alt="Select new App ID"/></center>

2. Scroll down to the Push Notifications section. Here you will be able to create both a Development SSL Certificate, as well as a Production SSL Certificate. Start by selecting "Create Certificate" under "Development SSL Certificate".

<center><img src="https://www.evernote.com/shard/s6/sh/9e2fbb56-ac52-433b-85d5-007563814584/e697d0a1883a83551ed1d434363b33a3/deep/0/Screenshot%204/23/13%2010:30%20AM.jpg" alt="Create Certificate"/></center>

3. The next screen will show instructions for creating a Certificate Signing Request (CSR). This is the same .certSigningRequest file you created earlier in Section 1.1. Select "Continue", then select "Choose File..." and locate the .certSigningRequest you created in Section 1.1.

4. Select "Generate". Once the certificate is ready, select "Done" and download the generated SSL certificate from the "iOS App ID Settings" screen.

<center><img src="https://www.evernote.com/shard/s6/sh/dfddfb66-87bb-47fd-beae-993c9a448b8a/e802be3f6680defc76b0a19f309b18ea/deep/0/Screenshot%204/23/13%2010:38%20AM.jpg" alt="Download SSL Certificate"/></center>

5. Double click on the downloaded SSL certificate to install it in your Keychain.

6. In Keychain Access, under "My Certificates", find the certificate you just added. It should be called "Apple Development IOS Push Services: <YourBundleIdentifier>".

<center><img src="https://www.evernote.com/shard/s6/sh/768d6cf4-c51b-46ff-82e8-ce583582c274/0211c15219f8d44bdd6478484e9f6eb5/deep/0/Screenshot%204/23/13%2010:43%20AM.jpg" alt="Export P12 Certificate"/></center>

7. Right-click on it, select "Export Apple Development IOS Push Services:...", and save it as a .p12 file. You will be prompted to enter a password which will be used to protect the exported certificate. *Do not enter an export password when prompted!* Note that you might have to enter your OS X password to allow Keychain Access to export the certificate from your keychain.

<center><img src="https://www.evernote.com/shard/s6/sh/77be7fdf-748a-4067-b911-1c8b9ba59ecf/7bb6c6393a137fb22c5d2a5f9d1282df/deep/0/Screenshot%204/23/13%2010:45%20AM.jpg" alt="Export as .p12"/></center>

If the Personal Information Exchange (.p12) option is grayed out in the export sheet, make sure "My Certificates" is selected in Keychain Access. If that does not help, double check that your certificate appears under the **login** keychain. You can drag and drop it into **login** if needed.

Note that you've just enabled Push Notification for your app in development mode. Prior to releasing your application on the App Store, you will need to repeat steps 1 through 7 of this section, but select "Production Push SSL Certificate" in step 2 instead, as covered in Section 7.

# 2. Creating the Provisioning Profile
A Provisioning Profile authenticates your device to run the app you are developing. Whether you have created a new App ID or modified an existing one, you will need to regenerate your provisioning profile and install it. If you have trouble using an existing profile, try removing the App ID and setting it back. For this tutorial, we'll create a new one.

1. Navigate to the [Apple Developer Member Center](https://developer.apple.com/membercenter/index.action) website, and select [Certificates, Identifiers & Profiles](https://developer.apple.com/account/overview.action).

2. Select <a href="https://developer.apple.com/account/ios/profile/profileList.action">Provisioning Profiles</a> from the iOS Apps section.

3. Select the + button to create a new iOS Provisioning Profile.

4. Choose "iOS App Development" as your provisioning profile type then select "Continue". We will create Ad Hoc and App Store profiles later.

5. Choose the App ID you created in Section 1 from the drop down then select "Continue".

6. Make sure to select your iOS Development certificate in the next screen, then select "Continue".

7. You will be asked to select which devices will be included in the provisioning profile. Select "Continue" after selecting the devices you will be testing with.

8. Choose a name for this provisioning profile, such as "My Parse Push App Development Profile", then select "Generate".

9. Download the generated provisioning profile from the next screen by selecting the "Download" button.

10. Install the profile by double-clicking on the downloaded file.

This should open Xcode's Organizer in the Devices pane. Your new provisioning profile should appear in the Provisioning Profiles section of your Library. Make sure that the status for this profile is "Valid profile". If the profile is invalid, make sure that your developer certificate is installed in your Keychain.

Note that prior to submitting your app to the App Store, you will need to test push notifications in production. This will be covered in Section 7.

# 3. Configuring the Parse App
To use Push Notifications with Parse, you will need to enable this feature in your Parse app and upload the Push SSL certificate you created above.

1. Navigate to your Parse app on the Parse Dashboard, and select the "Settings" tab.

2. Select "Push" from the left-hand side menu and select "Select your certificate" under the "Apple Push Certificates" header. and locate the .p12 certificate you exported from your Keychain earlier.

<center><img src="http://files.parsetfss.com/1d148bf9-5583-4811-9db7-0abfe0ecbc01/tfss-1af04ef7-0c70-4788-a383-1fb75ccd53e8-image.jpg" alt="Push Settings"/></center>

By default, push notifications can only be sent from your Parse dashboard, from Cloud Code, or through the REST API. If you want your users to be able to send push notifications from the client SDKs, you will need to toggle the "Client push enabled?" option to "on" under the "Push Notification Settings" header. This is useful for applications like chat clients, where users need to send push messages. For this tutorial, select toggle the option to "on".

# 4. Configuring a Push Enabled iOS Application
We are finally ready to create our iOS application! Let's start by configuring the Xcode project settings. We'll need to make sure that both the App ID and the provisioning profile are set.

1. In the Info.plist file under the Supporting Files folder, modify the Bundle Identifier field to match your App ID's Bundle Identifier (ex. com.example.MyParsePushApp).

<center><img src="https://www.evernote.com/shard/s6/sh/0425160f-1f14-4a2f-a9a6-d82893586f86/6734362f0786c4ade932440c998a9b30/deep/0/Screenshot%204/23/13%2011:44%20AM.jpg" alt="Info.plist configuration"/></center>

2. Select your project file in the left-hand menu. Under Project, navigate to "Build Settings", and find (or search for) the "Code Signing Identity" field.

3. Set all values under this heading to match the provisioning profile installed earlier (ex. "My Parse Push App Development Profile").

4. On the left-hand side, select your project's name under "Targets". Again, navigate to "Build Settings" and find the "Code Signing Identity" field. Ensure that all values here also match the new provisioning profile.

# 5. Adding Code for a Push Enabled iOS Application

We are now ready to start programming. We need to make a few modification to the app delegate in order to receive push notifications.

To register the current device for push, call the method `[application registerForRemoteNotifications]` in the app delegate's `-application:didFinishLaunchingWithOptions:` method.

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  ...
  UIUserNotificationType userNotificationTypes = (UIUserNotificationTypeAlert |
                                                  UIUserNotificationTypeBadge |
                                                  UIUserNotificationTypeSound);
  UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:userNotificationTypes
                                                                           categories:nil];
  [application registerUserNotificationSettings:settings];
  [application registerForRemoteNotifications];
  ...
}
```

If the registration is successful, the callback method `-application:didRegisterForRemoteNotificationsWithDeviceToken:` in the application delegate will be executed. We will need to implement this method and use it to inform Parse about this new device.

```objective-c
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  // Store the deviceToken in the current installation and save it to Parse.
  PFInstallation *currentInstallation = [PFInstallation currentInstallation];
  [currentInstallation setDeviceTokenFromData:deviceToken];
  currentInstallation.channels = @[ @"global" ];
  [currentInstallation saveInBackground];
}
```

When a push notification is received while the application is not in the foreground, it is displayed in the iOS Notification Center. However, if the notification is received while the app is active, it is up to the app to handle it. To do so, we can implement the `[application:didReceiveRemoteNotification]` method in the app delegate. In our case, we will simply ask Parse to handle it for us. Parse will create a modal alert and display the push notification's content.

```objective-c
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
  [PFPush handlePush:userInfo];
}
```

You should now run your application (on your iOS device) to make sure everything is set up correctly. If it is, the first time you run this app you should see a modal alert requesting permission from the user to send push notifications.

# 6. Sending Push Notifications
Parse allows you to send push notifications from the Parse website, the web API as well as the Parse SDK.

## 6.1. Parse website
Let's start with the Parse website. By navigating to your Parse app and selecting the "Push Notifications" tab, you can use the text box to broadcast a message to "Everyone". Simply enter a message and click send! If you've installed the app on a device, you should see the notification appear within a few seconds.

## 6.2. REST API
You can use the Parse REST API to send push notifications to all iOS devices by sending a POST request. Here is an example of a broadcast notification containing the message "Hello World!" sent using curl. Detailed information about the required format can be found in the [REST documentation](https://parse.com/docs/push_guide#top/REST).

```bash
curl -X POST \
  -H "X-Parse-Application-Id: ${APPLICATION_ID}" \
  -H "X-Parse-REST-API-Key: ${REST_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": "ios"
        },
        "data": {
          "alert": "Hello World!"
        }
      }' \
  https://api.parse.com/1/push
```

## 6.3. From the App
You can also send push notifications directly from a mobile application. Remember that you need to have enabled this feature in the Parse app's settings tab by selecting "Yes" under the heading "Client push enabled?". There are several methods that can be called to send push notifications. You can consult the full list in the [iOS API documentation](https://parse.com/docs/ios/api/Classes/PFPush.html). Here is an example:

```objective-c
// Create our Installation query
PFQuery *pushQuery = [PFInstallation query];
[pushQuery whereKey:@"deviceType" equalTo:@"ios"];

// Send push notification to query
[PFPush sendPushMessageToQueryInBackground:pushQuery
                               withMessage:@"Hello World!"];
```

## 6.4. From Cloud Code

Finally, you can send pushes automatically from [Cloud Code](/docs/cloud_code_guide). The `Parse.Cloud.afterSave` method lets us execute arbitrary code after an object is saved successfully. For example, if you wanted to send a push whenever a `"Comment"` object is saved:

```javascript
Parse.Cloud.afterSave("Comment", function(request) {
  // Our "Comment" class has a "text" key with the body of the comment itself
  var commentText = request.object.get('text');

  var pushQuery = new Parse.Query(Parse.Installation);
  pushQuery.equalTo('deviceType', 'ios');

  Parse.Push.send({
    where: pushQuery, // Set our Installation query
    data: {
      alert: "New comment: " + commentText
    }
  }, {
    success: function() {
      // Push was successful
    },
    error: function(error) {
      throw "Got an error " + error.code + " : " + error.message;
    }
  });
});
```

In this tutorial, we've learned how to enable and use push notifications in an iOS application. We began by creating an App ID, generating an associated SSL certificate, and then linking this App ID to a new provisioning profile. Next, we configured the Parse app by uploading the SSL certificate. Afterwards, we created an iOS application and configured it to use the new App ID and provisioning profile. We then implemented a set of delegation methods used to register the app to use push. Finally, we looked at four ways to send push notifications to users: the Parse website, the REST API, the Parse iOS SDK, and Cloud Code.

If you run into any problems, take a look at the "Troubleshooting Tips" section below.

Read on to learn the necessary steps to get your app ready for the App Store.

# 7. Preparing for the App Store

You've configured your app to receive push notifications during development. Prior to submitting your app to the App Store, you will need to configure push notifications for distribution.

There are two types of distribution profiles: Ad Hoc, and App Store. You will need the latter to submit your app to the App Store, however it is good practice to test push notifications using an Ad Hoc profile prior to submitting your app.

## 7.1. Configuring your App for Distribution Push Notifications

1. In Section 1.3., you configured your App ID for Push Notifications in Development. Retrace steps 1 through 7, but select "Production Push SSL Certificate" in step 2 instead.

2. Your App ID should now be configured for both Development and Distribution push notifications. Make sure to download the new Production SSL Certificate from the App ID Settings screen.

<center><img src="https://www.evernote.com/shard/s6/sh/af205eb3-76b4-42c4-8501-3eaea47178f0/29917f7e1e7eb0b708c3296beff6bc63/deep/0/Screenshot%204/23/13%2012:16%20PM.jpg" alt="Configured for Development and Distribution"/></center>

3. Double click on the downloaded SSL certificate to install it in your keychain. Right-click on it and export it as a .p12 file. Again, don't enter an export password when prompted.

4. Go back to Section 2 and retrace steps 1 through 10, making sure to select "Ad Hoc" under Distribution in Step 4. You should also use a different name in Step 8, such as "My Parse Push App Ad Hoc Profile". This should help you distinguish between development and distribution profiles.

5. Retrace the steps from Section 3, and upload your exported Production .p12 certificate to Parse instead.

6. In Section 4, you configured your app to use a Development provisioning profile. Retrace your steps, but this time choose your new Distribution Ad Hoc provisioning profile instead.

7. Build and run your app on an iOS device. Verify that push notifications are delivered successfully.

Note that once you have uploaded a production push certificate to Parse, you will only be able to target devices using a distribution provisioning profile. Devices running an app signed with a development provisioning profile will need to install the newly provisioned build again.

## 7.2. Configuring your App for App Store Distribution

You have now confirmed that your app is configured correctly to receive distribution push notifications using an Ad Hoc provisioning profile. Now it's time to submit your app to the App Store.

1. Follow steps 1 through 10 from Section 2, making sure to select "App Store" under Distribution for Step 4. Note that this time around, since you will be submitting your app to the App Store, you can skip Step 7 (selecting test devices).

2. Go through Section 4 again, this time selecting your new App Store Distribution provisioning profile.

3. Build and archive your iOS app, then submit to the App Store.

# 8. Troubleshooting Tips:

Setting up Push Notifications is often a source of frustration for developers. The process is complicated and invites problems to happen along the way. If you run into issues, try some of [these troubleshooting tips](https://parse.com/docs/push_guide#troubleshooting/iOS).
