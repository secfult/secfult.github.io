---
layout: post
title: Analysing HTTP/s traffic on the latest version of an Android virtual device
categories: [security, analysis]
tags: [android, app, traffic, http, https]
fullview: true
comments: true
---


The following instructions were tested on Windows 10 (version 1809), software versions below. I wanted to try this in a VM but ran into issues with Android Studio and nested virtualisation. I will try to keep it up to date as time progresses and this set of instructions no longer acheives the intended goal. This will take 1-2 hours to set up.

Tested with the following
* Windows 10 Home (version 1803)
* Java (Version 8 Update 211) (32 and 64 bit)
* Java JDK 12.0.1 (64 bit)
* Android Studio 3.4
* Burp Suite v1.7.36
* APKTool 2.4.0


#### THE SETUP


##### Java setup
1. Install Java from [here](https://www.java.com/en/download/) (32 and 64 bit, possibly just 64 bit but this is untested)
2. Install JDK from [here](https://www.oracle.com/technetwork/java/javase/downloads/jdk12-downloads-5295953.html)
    
    2.1. I recommend adding JDK bin dir to path as these tools will be required for every app: C:\Program Files\Java\jdk-12.0.1\bin\

##### Burp Suite Setup
1. Download [Burp Suite for Windows](https://portswigger.net/burp/communitydownload)
2. Install and start
3. Go to Proxy -> Options, edit the proxy settings
4. Bind to all ports, tick "Support invisible proxying"
5. Change your browser proxy to 127.0.0.1:8080
6. Browse to http://burp, download the Android certificate for later

##### APKTool setup
We'll need this tool to decompile and recompile the APK
1. [Download APK tool](https://ibotpeaches.github.io/Apktool/install/) and follow setup instructions

##### Android Studio Setup
1. Download [Android Studio](https://developer.android.com/studio)
2. Run the Android Studio installer
   
   2.1. Leave "Android Virtual Device" ticked
3. Start Android Studio
4. Click OK
5. Click Next, (Darcula's cool)
6. Tick "Android Virtual Device" (note that the Android SDK is installed at this point)
7. Make coffee/tea because installation will take a while
8. Android Studio should be opened to a splash screen, click Start new project, "Add no activity"
9. Select minimum API level (won't effect anything because we're not developing)
10. Click Finish, full IDE will load up. 
11. Click the AVD Manager button in the top right menu (little phone with green Android logo)
12. Here you can define the type of device that you want to emulate. I went with Nexus 5X API 28 x86 running Android 9.0 and it seems to be going okay so far
13. Drag and drop the certificate you downloaded earlier onto the screen, it should save to the SD card
14. Import the certificate (OS-dependent but easy to find)
15. In the settings toolbar to the right of the phone, click the ellipses at the bottom, go to Settings
16. Go to Proxy, set to manual, 127.0.0.1:8080
17. Click Apply, and it should say Success
18. Try opening the browser and visiting https://google.com and make sure you can see plaintext traffic in Burp


#### THE PROCESS
1. Download an APK. I use, but do not endorse https://apkcombo.com/ (you may have to rename the downloaded file to whatever.apk)
2. `apktool d whatever.apk -o whatever` (will create a folder called whatever will decompiled resources)
3. Modify `AndroidManifest.xml`, add `android:networkSecurityConfig="@xml/network_security_config"` attribute to application element so it will look like 
	```<application android:networkSecurityConfig="@xml/network_security_config" blahblahblah>```
4. Create file `/res/xml/network_security_config.xml` with following content:
    ```
    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config>
        <base-config>
            <trust-anchors>
                <certificates src="system" />
                <certificates src="user" />
            </trust-anchors>
        </base-config>
    </network-security-config>
    ```
5. `apktool b whatever -o whatever_patched.apk`
6. Follow [these instructions](https://gist.github.com/unoexperto/80694ccaed6dadc304ad5b8196cbbd2c) for signing the file (this is where adding the jdk bin to path will make it easier)
Output should be "jar signed."
7. Drag and drop the patched APK onto the AVD screen and it should automatically install!

Happy hacking!


#### THE PROBLEMS
##### Android Studio troubleshooting
AVD won't boot - Delete all .img files in `C:\Users\<username>\.android\avd\<problem device>`

APK fails to install with no error message displayed in AVD. The following tool will attempt to install and give you a more helpful error

    PS C:\Users\<username>\AppData\Local\Android\Sdk\platform-tools> .\adb.exe install "C:\APKs\APP\APP.apk"    
    Performing Streamed Install
    adb: failed to install C:\APKs\APP\APP.apk: Failure [INSTALL_PARSE_FAILED_NO_CERTIFICATES: Failed to collect certificates from /data/app/vmdl995408838.tmp/base.apk: Attempt to get length of null array]


##### APKTool troubleshooting
When building, you get the following exception: 
    
    brut.androlib.AndrolibException: brut.common.BrutException: could not exec (exit code = -1073741515)

Solution: Install 64 bit JRE
