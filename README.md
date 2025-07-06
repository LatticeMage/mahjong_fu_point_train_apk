# mahjong_fu_point_train

## steps
Debug on Android

run flutter
```
flutter run
```

## 1. Generate a Release Keystore

In your terminal, run (you’ll need the JDK’s `keytool`):

```bash
keytool -genkey \
  -v \
  -keystore ~/my-release-key.jks \
  -keyalg RSA \
  -keysize 2048 \
  -validity 10000 \
  -alias my_key_alias
```

## 2. Securely Store Key Info

Inside your Flutter project, create a file called `android/key.properties` (add this file to `.gitignore`):

```properties
storePassword=YOUR_KEYSTORE_PASSWORD
keyPassword=YOUR_KEY_PASSWORD
keyAlias=my_key_alias
storeFile=/absolute/path/to/my-release-key.jks
```

## 3. Configure Gradle to Sign with Your Keystore

Edit `android/app/build.gradle`. Inside the `android { ... }` block, add:

```groovy
def keystoreProperties = new Properties()
def keystorePropertiesFile = rootProject.file("key.properties")
if (keystorePropertiesFile.exists()) {
    keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
}

android {
    …

    signingConfigs {
        release {
            keyAlias keystoreProperties['keyAlias']
            keyPassword keystoreProperties['keyPassword']
            storeFile file(keystoreProperties['storeFile'])
            storePassword keystoreProperties['storePassword']
        }
    }

    buildTypes {
        release {
            // Enables code shrinking, obfuscation, and optimization for release builds
            minifyEnabled true
            // Uses the signing config defined above
            signingConfig signingConfigs.release
            // Recommended: include ProGuard rules (or R8)
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}
```

## Build Release APK or App Bundle

Now run one of:

* **APK**

  ```bash
  flutter build apk --release
  ```
* **App Bundle**

  ```bash
  flutter build appbundle --release
  ```