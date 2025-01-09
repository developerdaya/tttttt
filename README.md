Step-by-Step Guide: Setting Up and Integrating SSO SDK in React
Native
**Step-by-Step Guide: Setting Up and Integrating SSO SDK in React Native**
---
### 1. **Setup React Native Project**
1. **Initialize a React Native Project**
```sh
npx react-native init AwesomeProject
cd AwesomeProject
npm install
```
2. **Install Necessary Dependencies**
- Ensure `Node.js`, `React Native CLI`, and `Android Studio` are installed.
- Run the following commands to verify your setup:
```sh
node -v
npm -v
npx react-native --version
```
3. **Start the Development Server**
```sh
npx react-native start
```
---
### 2. **Add SSO SDK Dependency**
- Open `android/app/build.gradle` and add the following dependency in the `dependencies` block:
```gradle
dependencies {
implementation 'com.gitlab.shashwat-vik:android-sso-sdk:1.10'
}
```
---
### 3. **Create the MainActivity**
- Add the following Kotlin code in `android/app/src/main/java/com/awesomeproject/MainActivity.kt`:
```kotlin
package com.awesomeproject
import com.facebook.react.ReactActivity
import com.facebook.react.ReactActivityDelegate
import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint.fabricEnabled
import com.facebook.react.defaults.DefaultReactActivityDelegate
class MainActivity : ReactActivity() {
override fun getMainComponentName(): String = "AwesomeProject"
override fun createReactActivityDelegate(): ReactActivityDelegate =
DefaultReactActivityDelegate(this, mainComponentName, fabricEnabled)
}
```
---
### 4. **Create the MainApplication**
- Add the following Kotlin code in
`android/app/src/main/java/com/awesomeproject/MainApplication.kt`:
```kotlin
package com.awesomeproject
import android.app.Application
import com.facebook.react.PackageList
import com.facebook.react.ReactApplication
import com.facebook.react.ReactHost
import com.facebook.react.ReactNativeHost
import com.facebook.react.ReactPackage
import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint.load
import com.facebook.react.defaults.DefaultReactHost.getDefaultReactHost
import com.facebook.react.defaults.DefaultReactNativeHost
import com.facebook.react.soloader.OpenSourceMergedSoMapping
import com.facebook.soloader.SoLoader
class MainApplication : Application(), ReactApplication {
override val reactNativeHost: ReactNativeHost =
object : DefaultReactNativeHost(this) {
override fun getPackages(): List<ReactPackage> =
PackageList(this).packages.apply {
add(SsoSdkPackage())
}
override fun getJSMainModuleName(): String = "index"
override fun getUseDeveloperSupport(): Boolean = BuildConfig.DEBUG
override val isNewArchEnabled: Boolean = BuildConfig.IS_NEW_ARCHITECTURE_ENABLED
override val isHermesEnabled: Boolean = BuildConfig.IS_HERMES_ENABLED
}
override val reactHost: ReactHost
get() = getDefaultReactHost(applicationContext, reactNativeHost)
override fun onCreate() {
super.onCreate()
SoLoader.init(this, OpenSourceMergedSoMapping)
if (BuildConfig.IS_NEW_ARCHITECTURE_ENABLED) {
load()
}
}
}
```
---
### 5. **Create the SSO SDK Module**
- Add the following code in `android/app/src/main/java/com/awesomeproject/SsoSdkModule.kt`:
```kotlin
package com.awesomeproject
import android.content.Context
import com.edugorilla.ssologin.EdugorillaSSO
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.bridge.ReactContextBaseJavaModule
import com.facebook.react.bridge.ReactMethod
import org.json.JSONObject
class SsoSdkModule(reactContext: ReactApplicationContext) :
ReactContextBaseJavaModule(reactContext) {
private val context: Context = reactContext.applicationContext
override fun getName(): String {
return "SsoSdkModule"
}
@ReactMethod
fun initializeBaseUrlAndFileLocation(client_base_url: String) {
val aes_secret_key_location = R.raw.private_key
EdugorillaSSO.initializeBaseUrlAndFileLocation(client_base_url, aes_secret_key_location)
}
@ReactMethod
fun encryptUrlAndOpenWebView(userInfo: String, redirectUrl: String) {
val user_info: JSONObject = JSONObject(userInfo)
EdugorillaSSO.encryptUrlAndOpenWebView(context, user_info.toString(), redirectUrl)
}
}
```
---
### 6. **Create the SSO SDK Package**
- Add the following code in `android/app/src/main/java/com/awesomeproject/SsoSdkPackage.kt`:
```kotlin
package com.awesomeproject
import com.facebook.react.ReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.uimanager.ViewManager
import java.util.*
class SsoSdkPackage : ReactPackage {
override fun createNativeModules(reactContext:
com.facebook.react.bridge.ReactApplicationContext): List<NativeModule> {
return listOf(SsoSdkModule(reactContext))
}
override fun createViewManagers(reactContext:
com.facebook.react.bridge.ReactApplicationContext): List<ViewManager<*, *>> {
return Collections.emptyList()
}
}
```
---
### 7. **Add Private Key File**
1. **Create a Directory**
- Create a `res/raw` folder inside `android/app/src/main`.
2. **Paste the Key File**
- Place your `private_key` file inside the `res/raw` directory.
---
### 8. **Modify Android Manifest**
- Update `android/app/src/main/AndroidManifest.xml` to include the Internet permission:
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
<uses-permission android:name="android.permission.INTERNET" />
<application
android:name=".MainApplication"
android:label="@string/app_name"
android:icon="@mipmap/ic_launcher"
android:roundIcon="@mipmap/ic_launcher_round"
android:allowBackup="false"
android:theme="@style/AppTheme"
android:supportsRtl="true">
<activity
android:name=".MainActivity"
android:label="@string/app_name"
android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|screenSize|smallestSc
reenSize|uiMode"
android:launchMode="singleTask"
android:windowSoftInputMode="adjustResize"
android:exported="true">
<intent-filter>
<action android:name="android.intent.action.MAIN" />
<category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
</activity>
</application>
</manifest>
```
---
### 9. **Create the Index File**
- Add the following code in `index.js`:
```javascript
import {AppRegistry} from 'react-native';
import App from './App';
import {name as appName} from './app.json';
AppRegistry.registerComponent(appName, () => App);
```
---
### 10. **Create the App Component**
- Add the following code in `App.tsx`:
```tsx
import React from 'react';
import {SafeAreaView, Button, StyleSheet} from 'react-native';
import {NativeModules} from 'react-native';
const {SsoSdkModule} = NativeModules;
function App(): React.JSX.Element {
const startSSO = () => {
const userInfo = JSON.stringify({
name: 'Developer Daya',
email: 'developerdaya@gmail.com',
mobile: '7054576474',
});
const client_base_url = "https://mocktest.jagranjosh.com"
const redirect_url = "https://mocktest.jagranjosh.com";
SsoSdkModule.initializeBaseUrlAndFileLocation(client_base_url);
SsoSdkModule.encryptUrlAndOpenWebView(userInfo, redirect_url);
};
return (
<SafeAreaView style={styles.container}>
<Button title="Start SSO SDK" onPress={startSSO} />
</SafeAreaView>
);
}
const styles = StyleSheet.create({
container: {
flex: 1,
justifyContent: 'center',
alignItems: 'center',
backgroundColor: '#f8f9fa',
},
});
export default App;
```
---
### 11. **Run the Application**
- Use the following commands to build and run the app:
```sh
cd android
./gradlew sync
./gradlew build
npx react-native start
npx react-native run-android
```
