# [From zero to app](https://fromzerotoapp.com)

<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span>

## Android styling in Titanium using themes

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

Result is:
<img src="/images/android_theme02.png"/>

 https://github.com/tidev/titanium_mobile/discussions?discussions_q=theme

### Other examles

If posted some other guides in the `Show and tell` section in the [Github discussions](https://github.com/tidev/titanium_mobile/discussions?discussions_q=theme)
* [Custom color for Actionbar title/subtitle](https://github.com/tidev/titanium_mobile/discussions/13240)
* [Custom fonts in BottomNavigation](https://github.com/tidev/titanium_mobile/discussions/13060)
