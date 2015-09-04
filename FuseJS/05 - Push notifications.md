# Fuse Push Notifications

Fuse provides support for push-notifications from Google's Cloud Messaging (GCM) and Apple' Push Notification Service (APNS).

At present we opted for a lightweight consistent interface across iOS and Android which we can easily expand as needed.
We are very interested in comments & requests you have on what we have so far so do drop by the forums and let us know.

## Setting up the client side

### Step 1.
Include the Fuse push notification library by adding the following to your `.unoproj` file

    "Packages": [
        ...
        "Fuse.PushNotifications",
        ...
    ],

### Step 2. (Only for Android)
Google notifications require a little extra info. See the `Server Side` section below. Also you will need the google play libraries. To use those with Fuse please [see here](/developers/guides/installinggoogleplayservices)

Add the following to you `.unoproj`

```
"Android": {
    ...
    "GooglePlay": {
        "SenderID": "111781901112"
    }
    ...
},
```

Where the `SenderID` is the project ID from the `Google Developers Console`


## How this behaves in your app

What happens when your app starts varies slightly on each platform, but by referencing the Fuse package the following happens:

### Android
- 'Google Play' libraries are included in the build. (For info on getting the play libraries in Fuse click [here](/developers/guides/installinggoogleplayservices))
- Your SenderID is added to the project's `Manifest.xml` file along with some other plumbing
- When your app starts the app registers with the `GCM` service.

### iOS specific
- When your app starts it registers with APNS. As all access is controlled through Apple's certificate system there is no extra info to provide (we will mention server side a bit later)

### Both Platforms
- You get a callback telling you if the registration succeeded or failed.
- The succeeded callback will contain your unique registration id (also called a token in iOS docs)
- All future received push notifications will fire a callback containing the JSON of the notification.

All three callbacks mentioned are available in JavaScript and Uno.

## JavaScript code

Using the notifications from JS is super simple. Here is an example that just logs when the callbacks fire:

```
<FuseJS.Push ux:Global="Push" />
<JavaScript>
    var push = require("Push");

    push.onRegistrationSucceeded = function(regID) {
        console.log("Reg Succeeded: " + regID);
    };

    push.onRegistrationFailed = function(reason) {
        console.log("Reg Failed: " + reason);
    };

    push.onReceivedMessage = function(payload) {
        console.log("Recieved Push Notification: " + payload);
    };
</JavaScript>
```

In a real app you will want to send your `registration ID` to your server when `onRegistrationSucceeded` is called, but other than that this is fairly complete.

## Server Side

When we have our client all set up and ready to go we move on to the backend. We would have loved to be able to provide simplification here, but alas, you are required to jump through the hoops provided by Apple and Google.

Thankfully, *we do* have some guides for getting a setup you can use for testing.

iOS: [Setting up iOS Push Notifications Backend](/learn/guides/iospushnotification)
Android: [Setting up Android Push Notification Backend](/learn/guides/androidpushnotifications)

## The Notification
We support push notifications in JSON format. When a notification arrives one of two things will happen:

- If your app has focus, the callback is called right away with the full JSON payload
- If your app doesn't have focus, (and your JSON contains the correct data) we will add a system notification to the notification bar (called the Notification Center in iOS). When the user clicks the notification in the drop down then your app is launched and the notification is delivered.

Apple and Google's APIs define how the data in the payload is used to populate the system notification, however we have normalized it a little.

For iOS you just include an `'aps'` entry in the notification's JSON

```
'aps': {
    'title': 'Well would ya look at that!',
    'body': 'Hello from the server'
},
```

And 'title' and 'body' will be used as the title and body of the system notification.

For Android you can use exactly the same `'aps'` entry or the alternatively the following:

```
'notification': {
    'title': 'Well would ya look at that!',
    'body': 'Hello from the server'
},
```

The `'notification'` entry is the standard Google way of doing this but we felt that it wouldn't hurt to support the Apple way too.

The current implementation only guarantees the 'title' and 'body' entries will work. We also always use your app's icon as the notification icon. This is an area we will extend as Fuse matures. If you have specific requests, be sure to let us know!

Implementation Note:
Google and Apple has different limits on the size of push notifications.
- Google limits to 4096 bytes
- Apple limits to 2048 bytes on iOS 8 and up but only 256 bytes on all earlier versions
