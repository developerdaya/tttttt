### Requirement:
Connect to the support team to get the private_key for React Native SSO SDK. You can connect by using Phone no. 0522-3514751 or sending a mail over [support@edugorilla.org](https://mailto:support@edugorilla.org).

Once you get the private_key then proceed further.

---
### 1. **Add SSO SDK Dependency**
- Open `android/app/build.gradle` and add the following dependency in the `dependencies` block:
```gradle
dependencies {implementation 'com.gitlab.shashwat-vik:android-sso-sdk:1.10'}
```
---
### 2. **Create the MainActivity**
- Add the following Kotlin code in `android/app/src/main/java/com/your_project_name/MainActivity.kt`:
```kotlin
package com.your_project_name
import com.facebook.react.ReactActivity
import com.facebook.react.ReactActivityDelegate
import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint.fabricEnabled
import com.facebook.react.defaults.DefaultReactActivityDelegate
class MainActivity : ReactActivity() {
override fun getMainComponentName(): String = "your_project_name"
override fun createReactActivityDelegate(): ReactActivityDelegate =
DefaultReactActivityDelegate(this, mainComponentName, fabricEnabled)
}
```
---
### 3. **Create the MainApplication**
- Add the following Kotlin code in
`android/app/src/main/java/com/your_project_name/MainApplication.kt`:
```kotlin
package com.your_project_name
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
### 4. **Create the SSO SDK Module**
- Add the following code in `android/app/src/main/java/com/your_project_name/SsoSdkModule.kt`:
```kotlin
package com.your_project_name
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
### 5. **Create the SSO SDK Package**
- Add the following code in `android/app/src/main/java/com/your_project_name/SsoSdkPackage.kt`:
```kotlin
package com.your_project_name
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
### 6. **Add Private Key File**
1. **Create a Directory**
- Create a `res/raw` folder inside `android/app/src/main`.
2. **Paste the Key File**
- Place your `private_key` file inside the `res/raw` directory.
---
### 7. **Modify Android Manifest**
- Update `android/app/src/main/AndroidManifest.xml` to include the Internet permission:
```xml

    <uses-permission android:name="android.permission.INTERNET" />
    <application
    .....>
      <activity
        android:name=".MainActivity"
        android:label="@string/app_name"
        android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|screenSize|smallestScreenSize|uiMode"
        android:launchMode="singleTask"
        android:windowSoftInputMode="adjustResize"
        android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
      </activity>
</application>
```
---
### 8. **Create the Index File**
- Add the following code in `index.js`:
```javascript
import {AppRegistry} from 'react-native';
import App from './App';
import {name as appName} from './app.json';
AppRegistry.registerComponent(appName, () => App);
```
---
### 9. **Create the App Component**
- Add the following code in `App.tsx`:
```tsx
import React from 'react';
import {SafeAreaView, Button, StyleSheet} from 'react-native';
import {NativeModules} from 'react-native';
const {SsoSdkModule} = NativeModules;
function App(): React.JSX.Element {
const startSSO = () => {
const userInfo = JSON.stringify({
name: 'DeveloperDaya',
email: 'developerdaya123@gmail.com',
mobile: '9988774455',
});
const client_base_url = "https://stgtestseries.edugorilla.com"
const redirect_url = "https://stgtestseries.edugorilla.com";
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

### Congratulation! Your integration is finished.
This is our sample App:
[https://gitlab.com/shashwat-vik/react-native-sdk-example](https://gitlab.com/shashwat-vik/react-native-sdk-example)

For any query you feel free to contact our support team on

0522-3514751.

[support@edugorilla.org](https://mailto:support@edugorilla.org).

