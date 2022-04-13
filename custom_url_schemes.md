# [From zero to app](https://fromzerotoapp.com)

<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span> <a href="https://github.com/sponsors/m1ga"><img src="./images/gh_sponsor.png" title="Sponsor me"/></a>

# Using custom url schemes to start your app

You can start your app using a link on a webpage by calling a custom url. Those `custom url schemes` are links that start with a custom name like `mycustomapp://` and you can add parameter to it that will be forwarded to your app.

In your web page you add a simple link like this:
```html
<a href="mycustomapp://?data=customValue">custom link</a>
```
and we will prepare the Titanium app to listen to those links, open the app and display the parameters.

## Android

In order for Android to know that it has to start your app we will need to add the following `<intent-filter>` to your main `<activity>` in tiapp.xml:


```xml
<application>
	<activity android:name=".AppnameActivity">
		<intent-filter>
			<action android:name="android.intent.action.VIEW"/>
			<category android:name="android.intent.category.DEFAULT"/>
			<category android:name="android.intent.category.BROWSABLE"/>
			<data android:scheme="mycustomapp"/>
		</intent-filter>
	</activity>
</application>
```

You can change the `android:scheme` value to your custom url and you will need to change `android:name=".AppnameActivity"` so it will use your activity name. When you build your app you see a log like this:
```
[INFO]  TiRootActivity: (main) [0,0] checkpoint, on root activity resume. activity = com.miga.app.AppnameActivity@2a1c1c5
```
with your activity name at the end (it should be your app name without spaces).

## App code
```js
const win = Ti.UI.createWindow();
const lbl = Ti.UI.createLabel();

Ti.App.addEventListener("resume", function() {
	var data;
	if (OS_IOS) {
		data = Ti.App.arguments.url;
	} else {
		data = Ti.Android.currentActivity.intent.data;
	}
	lbl.text = data;
});

win.addEventListener("open", function() {
	var data;
	if (OS_IOS) {
		data = Ti.App.arguments.url;
	} else {
		data = Ti.Android.currentActivity.intent.data;
	}
	lbl.text = data;
});
win.add(lbl);
win.open();
```
