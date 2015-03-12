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
Manifest file changes
In the manifest file, for the activity that includes our fragment, please ensure that the following properties are defined as follows. You may add any other additional properties as you desire.

<activity
    android:name="com.example.PaymentActivity"
    android:screenOrientation="portrait"
    android:windowSoftInputMode="adjustResize">
</activity>
