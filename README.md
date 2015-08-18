# AndroidNetworkVulnerability

##Vulnerability

The following permissions are granted to the application even when they haven't been explicitely defined in the manifest file:

Protection level: normal
- android.permission.ACCESS_NETWORK_STATE
- android.permission.GET_ACCOUNTS
- android.permission.READ_EXTERNAL_STORAGE

Protection level: dangerous
- android.permission.ACCESS_COARSE_LOCATION
- android.permission.INTERNET
- android.permission.USE_CREDENTIALS
- android.permission.WRITE_EXTERNAL_STORAGE

This vulnerability only happen with a Phone/Wear project. With a regular Phone application, none of the permissions are granted (as expected, the permissions need to be defined in the manifest file) and the socket communication example below return an exception "SocketException: socket failed: EACCES (Permission denied)".

The funny thing is that it is not needed to publish both the "mobile" and the "wear" modules for the vulnerabily to happen, it only require to build the "mobile" module, install the apk on a smartphone running Android 5.1 and the permissions are granted by default.

##Development environment

* OS X 10.10.4
* Android Studio 1.2.2
* Android phone/tablet: API 22: Android 5.1 (Lollipop) (Tested on Nexus 4, 6, and 9)
* Android wear: API 21: Android 5.0 (Lollipop)

##Example: Exploit android.permission.INTERNET

###Vulnerability

Sending and receiving data with socket is possible without internet permission in manifest file.

###Problem

Without the explicit permission "android.permission.INTERNET" defined in the "AndroidManifest.xml" file, socket connections to a remote server are still possible. This issue allow any program to send and receive data over the internet without the user explicitly granting this permission to the application. Both user interaction or program thread can triggered incoming/outgoing communication.

```java
String permission = "android.permission.INTERNET";
int result = getApplicationContext().checkCallingOrSelfPermission(permission);
// Print "true" even though the internet permission is not defined in the manifest file
Log.d(this.getClass().getName(), "Internet permission granted: " + (result == PackageManager.PERMISSION_GRANTED));
```

According to Google's guidelines, any dangerous permissions requested by an application may be displayed to the user and require confirmation before proceeding. The permission allowing applications to open network sockets is categorized as "dangerous".

```xml
	<permission android:name="android.permission.INTERNET"
        android:permissionGroup="android.permission-group.NETWORK"
        android:protectionLevel="dangerous" />
```

This vulnerability allow any attacker to ship a legit application on Google Play store with a backdoor allowing communication with its server without the user permission. The number of possible attacks leveraged by such a vulnerability can lead to serious threats (privacy leak, user tracking, remote code execution, DDoS, and many more...).

###Steps to reproduce

1) Create project Phone/Wear project on Android Studio:
File > New Project
- Select: Phone And Tablet with Minimum SDK "API 22: Android 5.1 (Lollipop)"
- Select: Wear with Minimum SDK "API 21: Android 5.0 (Lollipop)"
- Select Blank Activity for both Phone and Wear

2) In the module "mobile", replace the body of MainActivity.java with the provided code and get the class PermissionScanner.java.
Change server address and port constants according to your specific configuration.

3) To prove that a communication channel is successfully established, get your server up and running.

4) Compile the module "mobile" (from Android Studio or Gradle), install, and run the application on a compatible device.

5) Here you go, your application is now able to send/receive messages to/from your remote server while no permission was defined in the manifest file to allow this.
