---
layout: post
title: "Setup Android CI on AWS or Azure - part 3"
description: ""
category: 
tags: []
---

## Install Android tools on VM

SSH to the VM

* #### Install SDK tools

```
$ cd /opt
$ sudo wget <latest android sdk>
``` 

<span style="color:grey"> eg.
$ sudo wget http://dl.google.com/android/android-sdk_r24.4.1-linux.tgz</span>


```
$ sudo tar zxvf <downloaded android sdk .tgz file>
$ sudo rm <downloaded android sdk .tgz file>
```

* #### Add Android SDK into environment variable 

```
$ cd /etc/profile.d
$ sudo vim android.sh
```
Add following into android.sh

```
export ANDROID_HOME="/opt/android-sdk-linux"
export PATH="$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$PATH"
```

Add Repositories config

```
$ sudo vim /var/lib/jenkins/.android/repositories.cfg
```

Add following content into repositories.cfg

```
### User Sources for Android SDK Manager
count=0
```

* #### Install Android SDK and platform compontents

```
$ cd /opt/android-sdk-linux/tools/
```

List all the sdk tools

```
$ android list sdk --all
```

Change to super user

	$ sudo su
	
Select the tools needed and platforms' number 
	
	$ android update sdk -u --all --filter <number>
	
Here is the list for reference
	
* Android SDK Tools, revision 25.2.3
* Android SDK Platform-tools, revision 25.0.1
* Android SDK Build-tools, revision 25.0.1
* SDK Platform Android 7.1.1, API 25, revision 2
* Google Play services, revision 38
* Google Repository, revision 40
* Android Support Repository, revision 40

* #### Install Git

```
$ sudo apt-get install git-core
```

* #### Install Oracle JDK 8

```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java8-installer
```	
	
* #### Install 32bit compatible libs for Anroid emulator

```
$ sudo apt-get install -y libc6-i386 lib32stdc++6 lib32gcc1 lib32ncurses5 lib32z1
```

* #### Restart VM

```
$ sudo shutdown -r now
```
	

* #### Copy Android License files from LOCAL Machine to VM

Due to some SDK components require UI to accept license and it is not applicable on VM command line. Do following command from you local android SDK folder.

```
user@Local:~$ cd ~/Library/Android/sdk
user@Local:~/Library/Android/sdk$ scp -r licenses ubuntu@<server_ip>:/opt/android-sdk-linux/ 
```

If you meet permission denied, on VM try 

```
$ sudo chmod 777 /opt/android-sdk-linux/ -R 
``` 

-----