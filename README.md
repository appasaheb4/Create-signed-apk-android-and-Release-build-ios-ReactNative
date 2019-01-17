# Create-signed-apk-android-and-Release-build-ios-ReactNative
How to create signed apk android and release build ios in react native ?


### android/app

sudo keytool -genkey -v -keystore testing.keystore -alias testing -keyalg RSA -keysize 2048 -validity 10000<br><br>

### android/gradle.properties

MYAPP_RELEASE_STORE_FILE=testing.keystore<br>
MYAPP_RELEASE_KEY_ALIAS=testing<br>
MYAPP_RELEASE_STORE_PASSWORD=developer<br>
MYAPP_RELEASE_KEY_PASSWORD=develoepr<br>

### android/app/build.gradle

```
...
android {
...
defaultConfig { ... }

signingConfigs {
release {
storeFile file("testing.keystore")  
 storePassword "developer"
keyAlias "testing"  
 keyPassword "developer"  
 }  
 }
 ```
<br>
===========> OR <<===============<br>

```
signingConfigs {
release {
if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
storeFile file(MYAPP_RELEASE_STORE_FILE)
storePassword MYAPP_RELEASE_STORE_PASSWORD
keyAlias MYAPP_RELEASE_KEY_ALIAS
keyPassword MYAPP_RELEASE_KEY_PASSWORD
}
}
}
buildTypes {
release {
aaptOptions.cruncherEnabled=false // here
minifyEnabled enableProguardInReleaseBuilds
proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
signingConfig signingConfigs.release
}
}
}
...
```
###
==> mkdir -p android/app/src/main/assets<br>
===> react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/<br>

Custom node_modules/react-native/react.gradle to solve the Duplicate file error perfectly. Add following code into currentBundleTask's creation block (after doFirst block)<br>

```
         doLast {
    def moveFunc = { resSuffix ->
        File originalDir = file("${resourcesDir}/drawable-${resSuffix}")
        if (originalDir.exists()) {
            File destDir = file("${resourcesDir}/drawable-${resSuffix}-v4")
            ant.move(file: originalDir, tofile: destDir)
        }
    }
    moveFunc.curry("ldpi").call()
    moveFunc.curry("mdpi").call()
    moveFunc.curry("hdpi").call()
    moveFunc.curry("xhdpi").call()
    moveFunc.curry("xxhdpi").call()
    moveFunc.curry("xxxhdpi").call()
}
```

cd android<br>
./gradlew assembleRelease

