# Work in progress 

# [From zero to app](https://github.com/m1ga/from_zero_to_app)

## Ah, push it

Extend you app with Firebase push notifications

![app with map](images/push_intro.png)

## Project setup

We will start with an empty Alloy project.

```bash
appc new
```

Have a look at the last section of the [first tutorial](./first_app.md#create-a-new---clean-project).

## Requirements

-   [Firebase Core](https://github.com/hansemannn/titanium-firebase) Module for Android / iOS
-   [Firebase Cloud Messaging](https://github.com/hansemannn/titanium-firebase-cloud-messaging) Module for Android / iOS

## Firebase setup

Go to <https://console.firebase.google.com/> and create a project.

-   create a new project:<br/>![create1](images/push_create1.png)

-   give it a name: <br/>![create2](images/push_create2.png)

-   click on iOS / Android: <br/>![create3](images/push_create3.png)

-   add your package name:<br/>![create4](images/push_create4.png)

-   download the config file: <br/>![create5](images/push_create5.png)

-   place it here:
-   iOS: place `GoogleService-Info.plist` into `app/assets/iphone`
-   Android: place `google-services.json` into `/app/assets/android/`

## XML setup

Download the modules and update your `tiapp.xml`:

```xml
<modules>
	<module platform="android">ti.playservices</module>
	<module>firebase.core</module>
	<module>firebase.cloudmessaging</module>
</modules>
```

```xml
<property name="ti.android.bug2373.finishfalseroot" type="bool">true</property>
```

## Code setup

At first you need to add the Firebase Core module and load the config file:

```javascript
// Require the Firebase Core module
var FirebaseCore = require('firebase.core');
FirebaseCore.configure();	// default google-services.json/GoogleService-Info.plist will be used
```

Then we add the Firebase Cloud Messaging module:

```javascript
var fcm = require('firebase.cloudmessaging');
if (OS_IOS) {
	Ti.App.iOS.addEventListener('usernotificationsettings', function eventUserNotificationSettings() {
		// Remove the event again to prevent duplicate calls through the Firebase API
		Ti.App.iOS.removeEventListener('usernotificationsettings', eventUserNotificationSettings);

		// Register for push notifications
		Ti.Network.registerForPushNotifications({
			success: function() { ...
			},
			error: function() { ...
			},
			callback: function() { ...
			} // Fired for all kind of notifications (foreground, background & closed)
		});

		// Register for Firebase Cloud Messaging
		fcm.registerForPushNotifications();
	});

	// Register for the notification settings event
	Ti.App.iOS.registerUserNotificationSettings({
		types: [
			Ti.App.iOS.USER_NOTIFICATION_TYPE_ALERT,
			Ti.App.iOS.USER_NOTIFICATION_TYPE_SOUND,
			Ti.App.iOS.USER_NOTIFICATION_TYPE_BADGE
		]
	});
} else {
	fcm.registerForPushNotifications();
}

fcm.addEventListener("didRefreshRegistrationToken", onToken);
fcm.addEventListener("didReceiveMessage", onMessage);

function onToken(e) {
	// if needed: send it to your server
	alert("new token" + e.fcmToken);
}

function onMessage(e) {
	console.log("Got message: " + JSON.stringify(e.message));
	alert("Got message: " + JSON.stringify(e.message));
}

console.log('FCM-Token: ' + fcm.fcmToken);
if (OS_ANDROID){
	console.log('last push data: ' + fcm.lastData);
}
```

## Test it

An easy test for FCM notifications is to use this little PHP script and execture it with `php test.php`

```php
<?php
    $url = 'https://fcm.googleapis.com/fcm/send';

    $fields = [
        'to' => 'DEVICE_TOKEN', // or topic: /topics/testTopic
        'notification' => [
            'title' => 'TiFirebaseMessaging',
            'body' => 'Message received'
        ],
        'data' => [
            'key1' => 'value1',
            'key2' => 'value2'
        ]
    ];

    $headers = [
        'Authorization: key=SERVER_ID_FROM_FIREBASE_SETTIGNS_CLOUD_MESSAGING', 'Content-Type: application/json'
    ];
    $ch = curl_init();

    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($fields));

    $result = curl_exec($ch);

    echo $result;
    curl_close($ch);
?>
```

Make sure to change the `DEVICE_TOKEN` and `SERVER_ID_FROM_FIREBASE_SETTIGNS_CLOUD_MESSAGING`

To get the server ID go back to the Firebase Cloud console:

* go to project settings:<br/>![create6](images/push_create6.png)
* switch to "Cloud Messaging" and copy the server key:<br/>![create7](images/push_create7.png)
