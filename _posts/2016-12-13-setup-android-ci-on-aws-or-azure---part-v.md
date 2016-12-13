---
layout: post
title: "Setup Android CI on AWS or Azure   part V"
description: ""
category: 
tags: []
---
## Add Android Emulator for Jenkins to run AndroidUnitTest

* Add libglu on VM

```
$ sudo apt-get install libglu1
```

* Install 
Google APIs ARM EABI v7a System Image, Android API 24, revision 10

```
$ android update sdk -a --no-ui --filter <number>
```

* Config Android Emulator Plugin on Jenkins (Only runnable on 3.5G Memory Azure)
	* In Jenkins Job Settings
	* **Build Enviroment** 
		* Check **Run emulator with properties**
		* [**Android OS Version**] *Android-24*
		* [**Screen density**] *160*
		* [**Screen resolution**] *HVGA*
		* [**Device locale**] *en_US*
		* [**SD card size**] *32M*
		* [**Target ABI**] *google_apis/armeabi-v7a*
		* [**Emulator name suffix**] *Nexus5*
		* Check **Reset emulator state at start-up**
	* **Build**
		* Check **Add Build Step** -> **Install Android Package**
		* [**APK file**] *app/build/outputs/apk/app-debug.apk*
		* Check **Add Build Step** -> **Run Android monkey tester**
		* [**Package ID**] *com.moroku.clients.smartsaver*
		* [**Event count**] 5000
		* [**Delay between event**] 100
		* Check **Add post Build action** -> **Publish Android monkey tester result
		* TODO AndroidUnitTest



## Done
New push to the Bitbucket Repo will trigger a Jenkins build now.

*[Note:] First time build may have error like*

> "A problem occurred configuring project ':app'.
> Failed to install the following SDK components:
> 
>   [Solver for ConstraintLayout 1.0.0-beta4, ConstraintLayout for Android 1.0.0-beta4]
  Please install the missing components using the SDK manager in Android Studio."


No Actions needs to do, try again, it will be installed automatically.