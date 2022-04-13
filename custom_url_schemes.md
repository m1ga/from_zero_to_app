# [From zero to app](https://fromzerotoapp.com)

<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span> <a href="https://github.com/sponsors/m1ga"><img src="./images/gh_sponsor.png" title="Sponsor me"/></a>

```xml
<activity android:name=".AppnameActivity">
	<intent-filter>
		<action android:name="android.intent.action.VIEW"/>
		<category android:name="android.intent.category.DEFAULT"/>
		<category android:name="android.intent.category.BROWSABLE"/>
		<data android:scheme="mycustomapp"/>
	</intent-filter>
</activity>
```

```html
<a href="mycustomapp://?data=customValue">custom link</a>
```

```js
Ti.App.addEventListener("resumed", function(event){
  var data;
  if (OS_IOS){
    data = event.url;
  } else {
    data = Ti.Android.currentActivity.intent.data;
  }
}
```
