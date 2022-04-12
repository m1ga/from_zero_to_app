# [From zero to app](https://fromzerotoapp.com)

<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span>

## Android styling in Titanium using themes

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Create a theme](#create-a-theme)
- [Use the theme](#use-the-theme)
- [How to style elements](#how-to-style-elements)
- [Examples](#examples)
  - [Arrow / menu item in Actionbar](#arrow--menu-item-in-actionbar)
  - [Toolbar (Titanium 10.2.0)](#toolbar-titanium-1020)
  - [Actionbar (Titanium 10.2.0)](#actionbar-titanium-1020)
  - [ListView headerTitle](#listview-headertitle)
- [Other examples](#other-examples)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Titanium allows you to use normal Android themes to style your entire application and customize elements that don't expose properties to Titanium. Make sure to read the [official Titanium guide](https://titaniumsdk.com/guide/Titanium_SDK/Titanium_SDK_How-tos/User_Interface_Deep_Dives/Android_UI_Components_and_Conventions/Android_Themes.html) first how to setup your basic theme. In this tutorial I will show you some styling examples.

_The good thing first_: you can search for native Android theme examples and put them into your theme file. _The "bad" part_: it can be very tricky to find the right style/parent property - even in native land! And since Titanium and most Android apps use Material now the chances are high that old stackoverflow posts or examples won't work anymore.

### Create a theme

We need a theme file first. Create a file called `mytheme.xml` in `/app/platform/android/res/values/` with:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="AppTheme" parent="Theme.Titanium.DayNight">
    </style>
</resources>
```

That is the basic Titanium theme with dark/night mode support and an action/status bar. You can use different parents from the [Titanium material themes](https://titaniumsdk.com/guide/Titanium_SDK/Titanium_SDK_How-tos/User_Interface_Deep_Dives/Android_UI_Components_and_Conventions/Android_Themes.html#titanium-material-themes) list.

### Use the theme

A theme can be global or just for specific windows.

For a global theme (all windows) you edit your `tiapp.xml` and add `android:theme` to the `<application>` block:

```xml
<android xmlns:android="http://schemas.android.com/apk/res/android">
    <manifest>
        <application android:theme="@style/AppTheme"/>
    </manifest>
</android>
```

If you just want to style some windows you can use the `theme` property:
```js
var win = Ti.UI.createWindow({theme: "AppTheme"});
```
You can also add it to `app.tss` to style all windows but have the possiblity to change it without changing the `tiapp.xml` file.


### How to style elements

In order to style elements you will need to know the item name and how to connect them. Basic elements are for example:
<img src="/images/android_theme01.png"/>

and you can put
```xml
<item name="colorPrimary">#1565C0</item>
```
into your `<styles>` section.
If you want to change `button` styles you have to create a style block for it. A whole list of items and their styles is in the [offical Android themes_material.xml](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/themes_material.xml) and have a look at the [Material components](https://material.io/components) document. You will find components like `button` and they have a `implementation` section with theming information: https://material.io/components/buttons/android#theming-buttons

That is most of the time the best and quickest way to find the correct style to theme your elements!

To style a button you can use:

```xml
<style name="AppTheme" parent="Theme.Titanium.DayNight">
    <item name="materialButtonStyle">@style/Widget.App.Button</item>
</style>

<style name="Widget.App.Button" parent="Widget.MaterialComponents.Button">
    <item name="android:padding">0dp</item>
    <item name="android:insetTop">0dp</item>
    <item name="android:insetBottom">0dp</item>
</style>
```

This will remove the extra padding/margin from the buttons.

### Examples


#### Arrow / menu item in Actionbar

To style the arrow or menu dots in the Actionbar you can use the following theme:

```xml
<style name="AppTheme" parent="Theme.Titanium.DayNight">
	<item name="actionBarTheme">@style/MyActionBar</item>
</style>

<style name="MyActionBar" parent="@style/Theme.Titanium.DayNight">
	<!-- arrow / menu dot color -->
	<item name="colorControlNormal">#ffffff</item>
	<!-- actionbar background -->
	<item name="android:background">#00B587</item>
</style>
```

<img src="/images/android_theme03.png"/>

<b>Note:</b>
Beginning with _10.2.0_ you can set a custom drawable as a `homeAsUpIndicator` (check https://github.com/tidev/titanium_mobile/pull/13149):


Place a drawable/icon into `/app/platform/android/res/drawble`.

file: `ic_baseline_close_24.xml`
```xml
<vector android:width="24dp" android:height="24dp" android:viewportWidth="24" android:viewportHeight="24" android:tint="?attr/colorControlNormal">
  <path android:fillColor="@android:color/white" android:pathData="M19,6.41L17.59,5 12,10.59 6.41,5 5,6.41 10.59,12 5,17.59 6.41,19 12,13.41 17.59,19 19,17.59 13.41,12z"/>
</vector>
```
and use this code:
```js
// Titanium 10.2.0 example:
const window = Ti.UI.createWindow();
window.activity.onCreate = () => {
	const actionBar = window.activity.actionBar;
	actionBar.displayHomeAsUp = true;
	actionBar.homeButtonEnabled = true;
	actionBar.homeAsUpIndicator = Ti.App.Android.R.drawable.ic_baseline_close_24;
	actionBar.onHomeIconItemSelected = () => {
		alert("Clicked on ActionBar home icon.");
	};
};
window.open();
```

<hr/>

#### Toolbar (Titanium 10.2.0)
_note: will work with Titanium 10.1.1.GA too but the drawable won't be tinted_

When you use a `Toolbar` and want to style the icon, arrow and text you can use the following example:

index.xml
```xml
<Alloy>
    <Window title="" customToolbar="toolbar" theme="AppTheme">
        <Toolbar id="toolbar" title="Title" subtitle="Subtitle" barColor="#004F9F" width="Ti.UI.FILL" top="0" displayHomeAsUp="true" homeButtonEnabled="true"/>
        <Menu >
            <MenuItem icon="Titanium.App.Android.R.drawable.cogwheel" showAsAction="Ti.Android.SHOW_AS_ACTION_ALWAYS"/>
        </Menu>
    </Window>
</Alloy>
```

and a `cogwheel.xml` file in `/app/platform/android/res/drawable/cogwheel.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="59.999998dp"
    android:height="60.903094dp"
    android:viewportWidth="59.999998"
    android:tint="?attr/colorControlNormal"
    android:viewportHeight="60.903094">

    <path
        android:fillColor="#ffffff"
        android:strokeWidth="0.0592785"
        android:pathData="M 26.880942,15.410922 c -0.302379,0 -0.755341,0.302531 -0.755341,0.604851 l -0.604851,4.08154 c -0.906959,0.302321 -1.662936,0.906043 -2.569895,1.510682 l -3.628143,-1.510682 c -0.45348,-0.151159 -0.75587,-3.76e-4 -1.058249,0.301944 l -3.023292,5.290278 c -0.15116,0.151161 5.88e-4,0.604475 0.302908,0.906795 l 3.173781,2.419405 c 0,0.45348 -0.150489,0.907007 -0.150489,1.511646 0,0.60464 -6.71e-4,1.057202 0.150489,1.510681 l -3.173781,2.569895 c -0.30232,0.302379 -0.302614,0.60538 -0.151454,0.907759 l 3.023292,5.290279 c 0.15116,0.151161 0.453256,0.302614 0.906795,0.151454 l 3.779597,-1.511646 c 0.755799,0.60464 1.66197,1.058166 2.56893,1.511646 l 0.604851,3.930087 c 0,0.302319 0.302825,0.603886 0.756306,0.603886 h 6.045619 c 0.302379,0 0.756305,-0.301625 0.756305,-0.603886 l 0.604851,-3.930087 c 0.90696,-0.45348 1.814095,-0.907006 2.569895,-1.511646 l 3.778633,1.511646 c 0.302379,0.15116 0.755575,-5.29e-4 0.906794,-0.302908 l 3.023292,-5.290278 c 0.15116,-0.30232 0.150925,-0.755635 -0.151454,-0.906795 l -3.476689,-2.419406 c 0,-0.453479 0.151454,-0.906041 0.151454,-1.510681 0,-0.604639 -2.94e-4,-1.058166 -0.151454,-1.511646 l 3.174746,-2.569894 c 0.302319,-0.30232 0.302614,-0.604475 0.151454,-0.906795 L 41.39255,20.247803 c -0.15116,-0.15116 -0.453256,-0.301649 -0.906795,-0.15049 l -3.779597,1.510682 c -0.7558,-0.604639 -1.662936,-1.057202 -2.569895,-1.510682 l -0.603886,-3.931051 c 0.15116,-0.453479 -0.302592,-0.75534 -0.604852,-0.75534 z m 3.023292,9.825216 c 2.872037,0 5.290278,2.419206 5.290278,5.291243 0,2.872038 -2.418241,5.290279 -5.290278,5.290279 -2.872038,0 -5.290279,-2.418241 -5.290279,-5.290279 0,-2.872037 2.418241,-5.291243 5.290279,-5.291243 z" />
</vector>
```

default theme (we have to disable the actionbar to use a toolbar):
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="AppTheme" parent="Theme.Titanium.DayNight.NoTitleBar"></style>
</resources>
```
<img src="/images/android_theme05.png"/>

<br/><br/>
Now we can use this theme to style each element:
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="AppTheme" parent="Theme.Titanium.DayNight.NoTitleBar">
		<item name="toolbarStyle">@style/Widget.App.Toolbar</item>
	</style>

	<style name="Widget.App.Toolbar" parent="Widget.MaterialComponents.Toolbar.Primary">
    <!-- title color -->
		<item name="titleTextColor">#f00</item>
    <!-- sub title color -->
		<item name="subtitleTextColor">#f0f</item>
    <!-- elements -->
		<item name="materialThemeOverlay">@style/ThemeOverlay.App.Toolbar</item>
	</style>

	<style name="ThemeOverlay.App.Toolbar" parent="">
    <!-- element tint color -->
		<item name="colorControlNormal">#ff0</item>
	</style>

</resources>
```
and we will get a wild color scheme:<br/>
<img src="/images/android_theme04.png"/>

<hr/>

#### Actionbar (Titanium 10.2.0)
_note: will work with Titanium 10.1.1.GA too but the drawable won't be tinted_

If we have the same setup as above but with an Actionbar:
```xml
<Alloy>
    <Window title="demoApp" customToolbar="toolbar" theme="AppTheme">
        <ActionBar homeButtonEnabled="true" displayHomeAsUp="true"/>
        <Menu >
            <MenuItem id="menu1" icon="Titanium.App.Android.R.drawable.cogwheel" showAsAction="Ti.Android.SHOW_AS_ACTION_ALWAYS" onClick="test"/>
        </Menu>
    </Window>
</Alloy>
```
<img src="/images/android_theme07.png"/>
<br/><br/>
we can use this theme:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="AppTheme" parent="Theme.Titanium.DayNight">
	</style>

	<style name="MyActionBar" parent="@style/Theme.Titanium.DayNight">
		<item name="colorControlNormal">#ff00ff</item>
		<item name="android:background">#004F9F</item>
		<item name="android:textColorPrimary">#ff0</item>
	</style>
</resources>
```
to get the same styling as with the Toolbar:
<img src="/images/android_theme06.png"/>

<hr/>

#### ListView headerTitle

When Titanium switched to use a RecyclerView for TableViews and ListViews they've also added more styling options to the header/footer titles. You have some custom attributes:
```xml
<attr name="ti_headerTitleSize" format="string" />
<attr name="ti_headerTitleStyle" format="string" />
<attr name="ti_headerTitleColor" format="color" />
<attr name="ti_headerTitleBackground" format="reference" />
<attr name="ti_headerTitleBackgroundColor" format="color" />
<attr name="ti_footerTitleSize" format="string" />
<attr name="ti_footerTitleStyle" format="string" />
<attr name="ti_footerTitleColor" format="color" />
<attr name="ti_footerTitleBackground" format="reference" />
<attr name="ti_footerTitleBackgroundColor" format="color" />
```
([source SDK](https://github.com/tidev/titanium_mobile/blob/b3256959bf84a6fb2e3e86554cdc0867f0d533c3/android/modules/ui/res/values/attrs.xml))
that you can style.

In your theme you can use them like this:
```xml
<style name="AppTheme" parent="Theme.Titanium.DayNight.Solid.NoTitleBar">
    <item name="ti_headerTitleSize">@string/ti_headerTitleSize</item>
    <item name="ti_headerTitleColor">@color/ti_headerTitleColor</item>
</style>

<string name="ti_headerTitleSize">60dip</string>
<color name="ti_headerTitleColor">#ff0000</color>
```

Result is:<br/>
<img src="/images/android_theme02.png"/>

 https://github.com/tidev/titanium_mobile/discussions?discussions_q=theme


### Other examples

If posted some other guides in the `Show and tell` section in the [Github discussions](https://github.com/tidev/titanium_mobile/discussions?discussions_q=theme)
* [Custom color for Actionbar title/subtitle](https://github.com/tidev/titanium_mobile/discussions/13240)
* [Custom fonts in BottomNavigation](https://github.com/tidev/titanium_mobile/discussions/13060)
