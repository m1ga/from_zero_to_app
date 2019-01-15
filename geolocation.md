# [From zero to app](https://github.com/m1ga/from_zero_to_app)

## Where am I?

Use geolocation services in your app

### XML setup

Add these parts to the tiapp.xml

iOS block:

```xml
<ios>
	<plist>
		<dict>
			<key>NSLocationWhenInUseUsageDescription</key>
			<string>[Why do you need the location?]</string>
			<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
			<string>[Why do you need the location?]</string>
		</dict>
	</plist>
</ios>
```

Android block:

```xml
<android xmlns:android="http://schemas.android.com/apk/res/android">
	<manifest>
		<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
		<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
	</manifest>
</android>
```

Modules block:

```xml
<modules>
	<module platform="android">ti.playservices</module>
</modules>
```

### Check for location permission at runtime:

```javascript
function getGeoPermission(clb) {
	var hasLocationPermissions = Ti.Geolocation.hasLocationPermissions(Ti.Geolocation.AUTHORIZATION_WHEN_IN_USE);
	Ti.API.info("Already got the permission? " + hasLocationPermissions);

	if (hasLocationPermissions) {
		Ti.API.info("has permission");
		clb();
		return;
	}
	Ti.Geolocation.requestLocationPermissions(Ti.Geolocation.AUTHORIZATION_WHEN_IN_USE, function(e) {
		if (e.success) {
			// yes
			Ti.API.info("ok")
			clb();
		} else if (OS_ANDROID) {
			// no
		} else {
			// no
		}
	});
}


getGeoPermission(function(){
	// callback function
})
```

### Register location events

```javascript
Ti.Geolocation.accuracy = Ti.Geolocation.ACCURACY_HIGH;
if (Ti.Geolocation.locationServicesEnabled) {
	if (OS_ANDROID) {
		// android
	} else {
		// ios
		Ti.Geolocation.accuracy = Ti.Geolocation.ACCURACY_BEST;
		Ti.Geolocation.distanceFilter = 2;
		Ti.Geolocation.preferredProvider = Ti.Geolocation.PROVIDER_GPS;
		Ti.Geolocation.pauseLocationUpdateAutomatically = true;
		Ti.Geolocation.activityType = Ti.Geolocation.ACTIVITYTYPE_FITNESS;
	}
	Ti.Geolocation.addEventListener('location', onLocation);
	Titanium.Geolocation.getCurrentPosition(onLocation);
}

function onLocation(e){
	Ti.API.info("location: " + JSON.stringify(e));
}
```


### Documentation

More information and properties can be found in the official documentation at https://docs.appcelerator.com/platform/latest/#!/api/Titanium.Geolocation
