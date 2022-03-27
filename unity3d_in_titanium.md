# _WORK IN PROGRESS - not done yet_

<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span>

# Using a Unity3D project inside a Titanum app
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [_WORK IN PROGRESS - not done yet_](#_work-in-progress---not-done-yet_)
- [Using a Unity3D project inside a Titanum app](#using-a-unity3d-project-inside-a-titanum-app)
  - [Introduction](#introduction)
  - [Tools](#tools)
  - [Unity3D](#unity3d)
  - [Android Studio](#android-studio)
  - [Appcelerator Titanium](#appcelerator-titanium)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

With Titanium you most of the time create "business apps" which use normal windows, texts, buttons, images,.... For games (2D or 3D) or apps that rely on graphical effects or use OpenGL you would use engines like Unity3D. While you can create UIs in Unity it is very time consuming if you want to recreate a native look and feel.

In this tutorial we will use the advantages of both engines and combine them together in one app. So you can use Titanium to create a native app with native UI elements and switch to a included Unity3D game with the click of a button.

## Tools

* Appcelerator Titanium 8.0.0 with Hyperloop
* Unity3D
* Android Studio

## Unity3D

Unity allows us to export an Android gradle project. To do this you need to make the following export adjustments:

* export android
* settings
* scripting backend: il2cpp
* switch on arm64

-> export

## Android Studio

Now you import the exported project into Android Studio to create a library instead of an app:

* open android studio
* import project folder as gradle project

* open build gradle
* change to `apply plugin: 'com.android.library'`
* remove `applicationId 'com.unity.game'`
* remove `bundle {..}`


* open manifest
* remove
```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
    <category android:name="android.intent.category.LEANBACK_LAUNCHER" />
</intent-filter>
```
* change theme to `"@style/Theme.Titanium"`
* make project

* copy `unity_project/build/outputs/aar/*.aar` to `app/platform/android/`

## Appcelerator Titanium

* add this to tiapp.xml
```xml
<android
    xmlns:android="http://schemas.android.com/apk/res/android">
    <manifest>
      <application>
        <activity android:name="com.unity.game.UnityPlayerActivity"/>
      </application>
    </manifest>
</android>
```

* add this to the controller

```javascript
//const unity = require("com.unity.game.UnityPlayerActivity");
var intent = Ti.Android.createIntent({
	className: "com.unity.game.UnityPlayerActivity",
});
Ti.Android.currentActivity.startActivity(intent);
```

* build your Titanium app
* run it
