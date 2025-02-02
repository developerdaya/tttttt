### Requirement:
Connect to the support team to get the private_key for React Native SSO SDK. You can connect by using Phone no. 0522-3514751 or sending a mail over [support@edugorilla.org](https://mailto:support@edugorilla.org).

Once you get the private_key then proceed further.

---
### 1. **Add SSO SDK Dependency**
- Open `android/app/build.gradle` and add the following dependency in the `dependencies` block:
```gradle
implementation 'com.gitlab.shashwat-vik:android-sso-sdk:1.10'
```
---
### 2. **Add Private Key File**
* Create a `res/raw` directory in `android/app/src/main`.  
* Place the `private_key` file inside it.
---
### 3. **Create the SSO SDK Module**

 * This module serves as a bridge between React Native and Android Edugorilla SSO SDK.
`android/app/src/main/java/com/your_project_name/SsoSdkModule.kt`:
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
---

### 4. **Create the SSO SDK Package**
* This file defines the SsoSdkPackage, which registers the SsoSdkModule with React Native.
* It ensures the native module is accessible from the JavaScript layer of the React Native app.
`android/app/src/main/java/com/your_project_name/SsoSdkPackage.kt`:
```kotlin
package com.your_project_name
import com.facebook.react.ReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.uimanager.ViewManager
import java.util.*

class SsoSdkPackage : ReactPackage {

  override fun createNativeModules(reactContext: com.facebook.react.bridge.ReactApplicationContext): List<NativeModule> {
    return listOf(SsoSdkModule(reactContext))
  }
  
  override fun createViewManagers(reactContext: com.facebook.react.bridge.ReactApplicationContext): List<ViewManager<*, *>> {
    return Collections.emptyList()
  }

}
```
---
### 5. **Modify the MainApplication**
- Add the SsoSdkPackage to include SDK functionality in the React Native application.

`android/app/src/main/java/com/your_project_name/MainApplication.kt`:
```kotlin
override fun getPackages(): List<ReactPackage> = PackageList(this).packages.apply {
  add(SsoSdkPackage())
}

```

### 6. **Initialize the Edugorilla SSO SDK from JavaScript**
```tsx
// Import the native module for integrating Edugorilla SSO SDK functionality.
import {NativeModules} from 'react-native';
const {SsoSdkModule} = NativeModules;

const startSSO = () => {
  const userInfo = JSON.stringify({
    name: 'John Doe',
    email: 'johndoe@example.com', 
    mobile: '1234567890',
  });

  const client_base_url = "your_base_url";
  const redirect_url = "your_redirect_page_url";

  SsoSdkModule.initializeBaseUrlAndFileLocation(client_base_url);
  SsoSdkModule.encryptUrlAndOpenWebView(userInfo, redirect_url);
};

<Button title="Start SSO" onPress={startSSO} />;

```

### Congratulation! Your integration is finished.
This is our sample App:
[https://gitlab.com/shashwat-vik/react-native-sdk-example](https://gitlab.com/shashwat-vik/react-native-sdk-example)


For any query feel free to contact our support team on
  * Phone: 0522-3514751
  * Email: [support@edugorilla.org](https://mailto:support@edugorilla.org)

