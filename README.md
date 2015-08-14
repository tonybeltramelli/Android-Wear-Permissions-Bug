# AndroidNetworkVulnerability

##Vulnerability

Sending and receiving data with socket is possible without internet permission in manifest file.

##Problem

Without the explicit permission "android.permission.INTERNET" defined in the "AndroidManifest.xml" file, socket connections to a remote server are still possible. This issue allow any program to send and receive data over the internet without the user explicitly granting this permission to the application. Both user interaction or program thread can triggered incoming/outgoing communication.

According to Google's guidelines, any dangerous permissions requested by an application may be displayed to the user and require confirmation before proceeding. The permission allowing applications to open network sockets is categorized as "dangerous".

```xml
	<permission android:name="android.permission.INTERNET"
        android:permissionGroup="android.permission-group.NETWORK"
        android:protectionLevel="dangerous" />
```

This vulnerability allow any attacker to ship a legit application on Google Play store with a backdoor allowing communication with its server without the user permission. The number of possible attacks leveraged by such a vulnerability can lead to serious threats (privacy leak, user tracking, remote code execution, DDoS, and many more...).

##Note

The problem proved to occur with a Phone/Wear application, a "traditional" Phone application require as expected the internet permission to be defined in the manifest file. 

##Development environment

OS X 10.10.4
Android Studio 1.2.2
Android phone/tablet: API 22: Android 5.1 (Lollipop)
Android wear: API 21: Android 5.0 (Lollipop)

##Steps to reproduce

1) Create project Phone/Wear project on Android Studio:
File > New Project
- Select: Phone And Tablet with Minimum SDK "API 22: Android 5.1 (Lollipop)"
- Select: Wear with Minimum SDK "API 21: Android 5.0 (Lollipop)"
- Select Blank Activity for both Phone and Wear

2) Replace the body of MainActivity.java of the module "mobile" with the provided code.
Basically just a java.net.Socket inside a java.util.concurrent.Executors.newCachedThreadPool().submit() block.
Change server address and port constants according to your specific configuration.

3) Get your server ready.

4) Compile module "mobile" (from Android Studio or Gradle), install, and run application.

5) Here you go, your application is now able to send/receive messages to/from your remote server while no permission was defined in the manifest file.
