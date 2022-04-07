# ESRC Face SDK for Android

[![Platform](https://img.shields.io/badge/platform-android-orange.svg)](https://github.com/esrc-official/ESRC-Face-SDK-Android)
[![Languages](https://img.shields.io/badge/language-java-orange.svg)](https://github.com/esrc-official/ESRC-Face-SDK-Android)
[![Commercial License](https://img.shields.io/badge/license-Commercial-brightgreen.svg)](https://github.com/esrc-official/ESRC-Face-SDK-Android/blob/master/LICENSE.md)

## Table of contents

  1. [About ESRC Face SDK](#about-esrc-face-sdk)
  1. [Install ESRC Face SDK](#install-esrc-face-sdk)
  1. [Making your first recognition](#making-your-first-recognition)

<br />

## About ESRC Face SDK

Through our **ESRC Face SDK** for Android, you can efficiently integrate real-time analysis of facial action unit and recognition of facial expression into your mobile app. This and other pages in the Getting Started provide the ESRC SDK’s structure and installation steps, then goes through the preliminary steps of implementing the ESRC Face SDK in your own project.

### Requirements

The minimum requirements for ESRC Face SDK for Android are:

- Android 4.2.0 (API level 17) or later
- Java 8 or later
- Gradle 4.0.0 or later

```groovy
// build.gradle(app)
android {
    defaultConfig {
        minSdkVersion 17
    }

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

### Key functions

|Function|Description|
|---|---|
|Measurement Environment Analysis| Analyze measurement environment including brightness. |
|Face Detection| Detect a single face using a front camera on mobile. |
|Facial Landmark Detection| Detect x and y coordinates of 68 facial landmarks in 2D space from the detected face. |
|Facial Action Unit Analysis| Extract centroid, area, theta and R distance of each 39 facial action unit from the detected 68 facial landmarks based on Facial Action Coding System determined by Paul Ekman. |
|Facial Expression Recognition| Recognize 7 facial expressions consist of neutral, happiness, sad-ness, surprise, anger, disgust and fear based on 6 basic emotions. |
|Head Pose Estimation| `(Coming Soon)` Estimate x, y and z angles of head pose in 3D space from the detected facial landmarks. |
|Attention Recognition| `(Coming Soon)` Recognize attention based on whether you are looking straight from head pose. |

### Try the sample app

Our sample app has the core features of the ESRC Face SDK. Download the app from our [GitHub repository](https://github.com/esrc-official/ESRC-Face-Android) to get an idea of what you can build with the actual SDK and start building in your project.

> Note: The fastest way to see our ESRC Face SDK in action is to build your app on top of our sample app. Make sure to change the application ID of the sample app to your own.

<br />

## Install ESRC Face SDK

This page provides a step-by-step guide that demonstrates how to build and configure an in-app face-analysis using the ESRC Face SDK. 

### Step 1: Download and install the third-party libraries (OpenCV, TensorFlow, RxJava)

Installing the OpenCV is simple if you’re familiar with using external libraries or SDKs. First, download the OpenCV `project` files from our [Google repository](https://drive.google.com/drive/folders/1QLduUkf0q2Yg4LwTUrr7EpwENNlea_Ep?usp=sharing).

Then, import the OpenCV `project` files as `opencv-3.4.11 module` in your app. Finally, add the following code to your module `build.gradle` file:
```gradle
allprojects {
	repositories {  
		...    
		maven { url "https://google.bintray.com/tensorflow" }
	}
}
```

> Note: Make sure the above code block isn't added to your module `bundle.gradle` file.

```groovy
dependencies {
    implementation project(path: ':opencv-3.4.11') // OpenCV
    implementation 'org.tensorflow:tensorflow-lite:0.0.0-nightly' // TensorFlow
    implementation 'io.reactivex.rxjava3:rxandroid:3.0.0' // RxJava
    implementation 'io.reactivex.rxjava3:rxjava:3.0.7' // RxJava    
}
```

### Step 2: Install the ESRC Face SDK

First, copy the ESRC Face SDK `.aar` file to the `app/libs` folder in your app. Then, add the dependency to your module `build.gradle` file:

```gradle
android {
    ...
    packagingOptions {
        pickFirst 'lib/arm64-v8a/*'
        pickFirst 'lib/armeabi-v7a/*'
        pickFirst 'lib/x86/*'
        pickFirst 'lib/x86_64/*'
    }
}

allprojects {
    ...    
    repositories {
        ...
        flatDir { dirs 'libs' }
    }
}
```

> Note: Make sure the above code block isn't added to your module `bundle.gradle` file.

```groovy
dependencies {
    implementation name: 'esrc-face-sdk-2.4.5', ext: 'aar'
}
```

### Step 3: Grant system permissions to the ESRC Face SDK

The ESRC Face SDK requires system permissions. These permissions allow the ESRC Face SDK to use a camera and read from and write on a user device’s storage. To grant system permissions, add the following lines to your `AndroidManifest.xml` file.

```manifest
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

The `CAMERA`, `READ_EXTERNAL_STORAGE` and `WRITE_EXTERNAL_STORAGE` permissions are classified as `dangerous` and require users to grant them explicitly when an app is run for the first time on devices running Android 6.0 or higher.

For more information about requesting app permissions, see  Android’s Request App Permissions [guide](https://developer.android.com/training/permissions/requesting.html).

<br />

## Making your first recognition

The ESRC Face SDK simplifies vision features into an effortless and straightforward process. To recognize your faccial action unit and facial expression, do the following steps:

This page provides a step-by-step guide that demonstrates how to build and configure an in-app face-analysis using ESRC Face SDK. License key can be received by requesting by the email: **esrc@esrc.co.kr**.

### Step 1: Initialize the ESRC Face SDK

Initialization binds the ESRC Face SDK to Android’s context, thereby allowing it to use a camera in your mobile. To the `init()` method, pass the **App ID** of your ESRC application to initialize the ESRC SDK and the **ESRCLicenseHandler** to received callback for validation of the App ID.

```java
ESRC.init(APP_ID, getApplicationContext(), new ESRCLicense.ESRCLicenseHandler() {
    @Override
    public void onValidatedLicense() {
        …
    }
    
    @Override
    public void onInvalidatedLicense() {
        …
    }
});
```

> Note: The `ESRC.init()` method must be called once across your Android app. It is recommended to initialize the ESRC Face SDK in the `onCreate()` method of the Application instance.

### (Optional) Step 2: Bind the ESRC Fragment

If you don't want to develop a layout that uses the camera, you can ues the ESRC Fragment provided from the ESRC SDK. Include the **container** to bind the ESRC Fragment in your layout `.xml` file. Please skip the Step 4: Feed the ESRC Face SDK. The ESRC Fragment will feed the image to our SDK itself.

```xml
<FrameLayout
    android:id=”@+id/container”
    android:layout_width=”match_parent”
    android:layout_height=”match_parent” />
```

> Note: FrameLayout is just one of examples. You can change to other layout type to purpose your app.

Bind the ESRC Fragment to display the image taken with the camera on the screen. ESRC Fragment send the image to the ESRC Face SDK in real-time to be able to analyze facial action unit and to recognize facial expression. ESRC Fragment automatically display the image to fit the size of your custom layout.

```java
// Bind LAYOUT.xml on your Activity.
setContentView(R.layout.LAYOUT);

// Replace the container in LAYOUT as the ESRC Fragment .
getSupportFragmentManager().beginTransaction()
    .replace(R.id.container, ESRCFragment.newInstance())
    .commit();
```

### Step 3: Start the ESRC Face SDK

Start the ESRC Face SDK to recognize your facial expression. To the `start()` method, pass the `ESRCType.Property` to select analysis modules and the `ESRC.ESRCHandler` to handle the results. You should implement the callback method of `ESRC.ESRCHandler` interface. So, you can receive the results of face, facial landmark, facial action unit, and facial expression. Please refer to **[sample app](https://github.com/esrc-official/ESRC-Face-Android)**.

```java
ESRC.start(
    new ESRCType.Property(
        true,  // Whether visualize result or not. It is only valid If you bind the ESRC Fragment (i.e., Step 2).
        true,  // Whether analyze measurement environment or not.
        true,  // Whether detect face or not.
        true,  // Whether detect facial landmark or not. If enableFace is false, it is also automatically set to false.
        true,  // Whether analyze facial action unit or not. If enableFace or enableFacialLandmark is false, it is also automatically set to false.
        true),  // Whether recognize facial expression or not. If enableFace is false, it is also automatically set to false.
    new ESRC.ESRCHandler() {
        @Override
        public void onDetectedFace(ESRCTYPE.Face face, ESRCException e) {
            if(e != null) {
                // Handle error.
            }
            
            // The face is detected.
            // Through the “face” parameter of the onDetectedFace() callback method,
            // you can get the location of the face from the result object
            // that ESRC Face SDK has passed to the onDetectedFace().
            …
        }
        
        // Please implement other callback method of ESRC.ESRCHandler interface.
        @Override public void onNotDetectedFace( … ) { … }
        @Override public void onAnalyzedMeasureEnv( … ) { … }
        @Override public void onDetectedFacialLandmark( … ) { … }
        @Override public void onAnalyzedFacialActionUnit( … ) { … }
        @Override public void onRecognizedFacialExpression( … ) { … }        
    });
```

### (Optional) Step 4: Feed the ESRC Face SDK

Feed `OpenCV Mat` on the ESRC Face SDK. To the `feed()` method, pass the `Mat` image received using a camera in real-time. Please do it at 10 fps. You can skip this step if you follow Step 2: Bind the ESRC Fragment.

```java
ESRC.feed(Mat);
```

### Step 5: Stop the ESRC Face SDK

When your app is not use the camera or destroyed, stop the ESRC Face SDK.

```java
ESRC.stop();
```
