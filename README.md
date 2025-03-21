# CheckBeer

A lightweight native library for Android app security and anti-tampering checks. Easily integrate signature verification and tampering detection into your Android apps.

> [!NOTE]
> Even though CheckBeer provides enhanced security, it is not a silver bullet. It is essential to follow security best practices and regularly update your app to maintain security effectiveness.

## Features

- Comprehensive security checks in native code
- Minimal integration requirements
- Customizable security validations
- Individual check functions for granular control

## Integration

### 1. Add Required Files

Copy these two files to your project's native code directory:

```plaintext
src/main/cpp/
├── CheckBeer.hpp
└── JNIHelper.hpp
```

### 2. Include Headers

In your native code, include the required headers:

```cpp
#include "CheckBeer.hpp"
#include "JNIHelper.hpp"
```

### 3. Implement Checks

You can use individual checks or the complete verification:

```cpp
// Complete signature verification
bool signatureBypass = checkSignatureBypass(env, context);
if (signatureBypass) {
    // Tampering detected
    // Handle security violation
}

// Or use individual checks as needed:
bool creatorCheck = checkCreator(env);
bool fieldCheck = checkField(env);
bool proxyCheck = checkPMProxy(env, context);
bool componentCheck = checkAppComponentFactory(env);
bool pathCheck = checkApkPaths(env, context);
```

## Available Security Checks

> [!NOTE]
> `checkSignatureBypass` performs all the following checks.

### 1. Package Creator Validation
```cpp
bool checkCreator(JNIEnv* env);
```
Validates the integrity of `PackageInfo`'s `Parcelable.Creator`

### 2. Field Structure Check
```cpp
bool checkField(JNIEnv* env);
```
Verifies the `CREATOR` class structure for tampering

### 3. Package Manager Proxy Detection
```cpp
bool checkPMProxy(JNIEnv* env, jobject context);
```
Ensures legitimate `IPackageManager` implementation

### 4. Component Factory Verification
```cpp
bool checkAppComponentFactory(JNIEnv* env);
```
Validates App Component instantiation

### 5. APK Path Integrity
```cpp
bool checkApkPaths(JNIEnv* env, jobject context);
```
Verifies APK location and permissions

## Logging

All checks provide detailed logging with the tag "CheckBeer":

```bash
adb logcat | grep CheckBeer
```

## Example Implementation

Here's a minimal example of using CheckBeer to verify app integrity in your native code:

```cpp
#include "CheckBeer.hpp"
#include "JNIHelper.hpp"

extern "C" JNIEXPORT jboolean JNICALL
Java_com_example_app_SecurityCheck_verifyIntegrity(
        JNIEnv* env,
        jobject obj,
        jobject context) {
    
    bool suspicious = checkSignatureBypass(env, context);
    return suspicious;
}
```

Corresponding Java code:

```java
public class SecurityCheck {
    
    public static native boolean verifyIntegrity(Context context);

    public void validateApp(Context context) {
        try {
            if (verifyIntegrity(context)) {
                throw new RuntimeException("Security violation detected!");
            }
        } catch (UnsatisfiedLinkError | Exception e) {
            // Handle errors if needed
        }
    }
}

```

## Demo

![Okay](https://github.com/user-attachments/assets/5b8e5bda-0577-4b10-9506-e8965a86a6c5)

![Wrong1](https://github.com/user-attachments/assets/0eba4f9d-93f0-4bbe-837f-25cf34838706)

![Wrong2](https://github.com/user-attachments/assets/a18579d2-eec8-4f75-951c-f3e5530d01cc)

![Wrong3](https://github.com/user-attachments/assets/43a57f33-d71d-4d3f-a2bc-32d316c3b3ab)


## License

This project is licensed under the MIT License.

## Acknowledgements

- [Android JNI Helper](https://github.com/reveny/Android-JNI-Helper) - Core JNI functionality
