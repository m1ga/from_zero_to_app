# [From zero to app](https://github.com/m1ga/from_zero_to_app)

## Debugging WebViews

In a previous blog post Ewan Harris was talking about how to debug in VS Code (<a href="https://devblog.axway.com/mobile-apps/vs-code-debugging-for-titanium/" target="_blank">Debugging in VS Code</a>) but from time to time you have to use a Webview insider your and with an own little app inside. Normally you would debug the web application you run inside the Webview in a normal browser and then just include it in your app. It might work at first try but sometimes the Webview displays your HTML part differently than the browser on your phone and you have to debug the Webview inside your app. Luckily both Android and iOS allow you to do that.


## The app

To demonstrate the debugging part we need a small app with a Webview and some HTML content. For this you can create a new Alloy app where the controller looks like this:

```javascript
$.index.open();
```

and the view contains this code:

```xml
<Alloy>
    <Window backgroundColor="#fff">
        <WebView id="www" url="/test.html" width="Ti.UI.FILL" height="Ti.UI.FILL"/>
    </Window>
</Alloy>
```

The last thing is a HTML file called test.html inside the /assets folder:
```html
<!DOCTYPE html>
<html>

<head>
    <meta name="viewport" content="width=device-width, user-scalable=no" />
</head>

<body>
    <img src="http://placehold.it/300x300"/>
    <p>Lorem ipsum dolor sit amet</p>
</body>

</html>
```


When you run the app it will look like this:
<img src="images/debug_1.png"/>

## Debugging iOS

At first we will use Safari to debug the iOS Webview. In the Safari settings you will need to enable the “Developer settings” first by setting this flag:

<img src="images/debug_2.png"/>

After that you will see all your devices and simulators inside the new “Develop” menu:

<img src="images/debug_3.png"/>

and you can click on test.html which is the Webview we have created earlier. This will open a full Safari Web Inspector with the content of your Webview Content and you can start debugging it:

<img src="images/debug_4.png"/>

Tip: you can even use the “Click to select” tool, even on a connected iPhone!

## Debugging Android

To debug an Android Webview we have to use the Chrome browser. At first we need to add the android:debuggable flag to tiapp.xml:

```xml
  <android xmlns:android="http://schemas.android.com/apk/res/android">
    <manifest>
      <application android:debuggable="true"/>
    </manifest>
  </android>
```

This will set the app into a debug mode (make sure to remove that again for your production app!) which allows Chrome to connect to the Webview. After you open Chrome you go into the DevTools (F12) and click on “More tools” menu and select “Remote devices”

<img src="images/debug_5.png"/>

Next switch on “Discover USB devices” and you should see you connected Android devices:

<img src="images/debug_6.png"/>

When you select your phone it will display the webpage again and you can click “Inspect” to open the DevTools:

<img src="images/debug_7.png"/>

Like on iOS it will show you the normal DevTools for the content of your Webview and you can start debugging it:

<img src="images/debug_8.png"/>
