# Getting started


## Platform & API Level support

Currently only android based applications are supported. Android versions >= API 8 are supported.

## Android Permissions

The following are the minimum set of permissions required for the library to work as expected:

```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

#### Additional permissions preferred:

```
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
<uses-permission android:name="android.permission.SEND_SMS"/>
```


In order to support sending SMS for handling CITI Bank, we will need SEND_SMS permission. However, this permission is optional. When this permission is not available, CITI Bank card transactions will not be enhanced by the library.

## Manifest file changes

In the manifest file, for the activity that includes our fragment, please ensure that the following properties are defined as follows. You may add any other additional properties as you desire.
```

<activity
    android:name="com.example.PaymentActivity"
    android:screenOrientation="portrait"
    android:windowSoftInputMode="adjustResize">
</activity>
```

Getting the SDK
The library can be added as a direct dependency when you use Gradle. If you don’t use Gradle, we provide a downloadable SDK which you can add as a project dependency.
Gradle
Add the following maven repository to the build.gradle. Please make sure that you add it in the project dependency section and NOT the buildScript section.

repositories {
    mavenCentral()
    maven { 
       url "https://s3-ap-southeast-1.amazonaws.com/godel-release/godel/"
    }
}

Add the following compile dependency to your project. Here again make sure you don’t accidentally add it to script dependencies. 

dependencies {
    compile 'com.android.support:support-v4:+'
    compile 'in.juspay:godel:0.5rc2'
}
Non Gradle (or Eclipse style integration) 

Download the binary from this location:

https://s3-ap-southeast-1.amazonaws.com/godel-release/stable/godel-0.5rc2.tar.gz

This can be added directly as a project dependency in Eclipse or Intellij Idea. Besides adding  the project as a dependency, you will have to add to the assets too. Once the library is extracted into a folder, navigate to godel/assets. Add everything in the folder to your src/main/assets folder. 

cp $GODEL_LIBRARY_PATH/assets/* $PROJECT_HOME/src/main/assets

You should add appcompat-v4 from Google SDK as a library project as well.