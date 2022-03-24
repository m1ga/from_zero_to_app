# [From zero to app](https://fromzerotoapp.com)

<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/miga" title="donate"><img src="https://img.shields.io/badge/buy%20me%20a%20coke-donate-orange.svg" alt="Buy Me A Coke donate button" /></a></span>

## Android Modules: using local aar files

Since Titanium SDK 9.0.0 the gradle build system is used to create the Android apps and modules.
This brings a lot of advantages makes it quicker and easier to build apps and modules (e.g. dependencies are handled automatically). You can use the `build.gradle` file to include external libraries into your modules or app.

In <a href="https://github.com/m1ga/ti.animation">Ti.Animation</a> we use:

```
repositories {
	google()
	jcenter()
}

dependencies {
  implementation 'com.airbnb.android:lottie:3.4.0'
}
```
to add the external library and with Android Studio you can even use full auto-completion when you write a Titanium module now!


### Local libraries

While this works great for external libs that are available at maven, google or another repository it doesn't work for local AARs (they are not build into the module, so not available in the app later on).

You still can use them but you have to add some extra work. Here are three different ways:

#### 1. Only a JAR in the AAR

An AAR file is just a compressed packaged including different files. If your AAR does not contain any "res" files or AndrodManifest.xml settings file you can just unzip the AAR and use the "classes.jar" instead. JARs will be merged into your module. Place them in the /lib folder and build your module.

##### AAR with res folder
Another approach is to unzip the AAR and copy its JAR, AndroidManifest.xml settings, and res files into the module project. While this is fine for simple AARs it is not recommended for comlex ones.

#### 2. Use the AAR

If there are other files inside the AAR file you have to include the whole file. Since it is not bundled in the compiled module you can add it to your app later on again.

As an example we will use the Polar BLE SDK (https://github.com/polarofficial/polar-ble-sdk). Download the AAR and put it into the module libs folder and add this to the `build.gradle` file:

```
repositories {
       maven { url "https://oss.jfrog.org/libs-snapshot" }
   }


dependencies {
    // old method; looks like newer gradles versions don't support this
    //
    // implementation files('../../libs/polar-ble-sdk.aar')
    // implementation files('../../libs/polar-protobuf-release.aar')

    // new version
    compileOnly files('../../libs/polar-ble-sdk-1.0.0.aar')
    compileOnly files('../../libs/polar-protobuf-release.aar')

    implementation 'io.reactivex.rxjava3:rxandroid:3.0.0'
    implementation 'io.reactivex.rxjava3:rxjava:3.0.0'
}
```

Inside the java source file (e.g. `TiPolarModule.java`) we add the following method:

```java
import polar.com.sdk.api.PolarBleApi;
import polar.com.sdk.api.PolarBleApiDefaultImpl;

@Kroll.method
public void create()
{
	PolarBleApi api = PolarBleApiDefaultImpl.defaultImplementation(TiApplication.getAppCurrentActivity(), PolarBleApi.FEATURE_HR);
	Log.i("Polar", "Version: " + PolarBleApiDefaultImpl.versionInfo());
}
```
so we can check that the module is working inside the app.

Now it is time to build the module and copy the zip into the Titanium app.

Inside the app you place the aar files into `./app/platform/android` and Titanium will automatically include all AARs/JARs in that folder.
Titanium's [build system copies](https://github.com/appcelerator/titanium_mobile/blob/master/android/cli/commands/_build.js#L2689-L2719) the `./platform/android` file tree to the generated Android app `./build/android/app/src/main` folder. Those AAR/JAR files are included by the global [app build.gradle](https://github.com/appcelerator/titanium_mobile/blob/master/android/templates/build/app.build.gradle#L98)

In your code file add this:

```javascript
var window = Ti.UI.createWindow({title: "Test"});
var polar = require("ti.polar");
polar.create();
window.open();
```

The output will be something like `[INFO]  Polar: Version: 2.2.2`.

#### 3. Use a local repository

create a local maven repo. It is just a folder (e.g. /home/miga/tmp/) with a pom.xml containing:
```xml
<repositories>
    <repository>
        <id>repository</id>
        <url>/home/miga/tmp/</url>
    </repository>
</repositories>
```

Then run

```
mvn install:install-file -Dfile=/home/miga/download/lib.aar -DgroupId=YOUR_GROUP_ID -DartifactId=YOUR_ARTIFACT_ID -Dversion=1.0.0 -Dpackaging=aar -DlocalRepositoryPath=/home/miga/tmp
```

and it will add the aar to the local repo.

In the module AND app add this to your build.gradle:
```
repositories {
    maven {
     url '/home/miga/tmp/'
    }
}
dependencies {
    implementation 'YOUR_GROUP_ID:YOUR_ARTIFACT_ID:1.0.0'
}
```
