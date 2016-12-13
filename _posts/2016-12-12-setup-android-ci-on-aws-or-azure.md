---
layout: post
title: "Setup Android CI on AWS or Azure"
description: ""
category: 
tags: []
---

## Creat the VM on Azure
* Login with your ID on Microsoft Azure. Select Virtual Machines a.k.a VM
![VM](../images/azure/VM.png)

* Add a new VM -> Choose **Ubuntu Server 16.04 LTS** 
* Ensure the deployment model is **Resource Manager**
![Ubuntu](../images/azure/ubuntu.png)

* Input basic properties of the VM
![basic](../images/azure/basic.png)

* Choose the VM Memory and CPU
![choose](../images/azure/choose2.png)

> 2G memory at least, if using Android Emulator for Android Unit Test, 3.5G+ memory should be selected.

* Change the IP to static IP and write down the server IP.
![IP](../images/azure/IP.png)

* Make the 8080 port accessable in **Network interface**
![port](../images/azure/port.png)

-------


## Install Jenkins on VM
* #### Login to the VM by IP from your local machine

```
$ ssh ubuntu@[SERVER_IP]
```

* #### Download and Install Jenkins
   
```
   $ wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
   $ sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
   $ sudo apt-get update
   $ sudo apt-get install jenkins
```
   Restart VM to apply upgrades
   
```
   $ sudo shutdown -r now
```
   Login to the server and upgrade packages then restart
   
```
   $ sudo apt-get upgrade
   $ sudo shutdown -r now
```
   
* #### Install Apache Webserver
	
```
	$ sudo apt-get install aptitude
	$ sudo aptitude install apache2
	$ sudo a2enmod proxy
	$ sudo a2enmod proxy_http
```

Change Jenkins port to 8080
	
```
	$ cd /etc/apache2/sites-available
	$ sudo vim jenkins.conf
```

Paste following code to the jenkins.conf file
	
```
	<VirtualHost *:80>
	 ServerAdmin webmaster@localhost
	 ServerName ci.company.com
	 ServerAlias ci
	 ProxyRequests Off
	 <Proxy *>
	  Order deny,allow
	  Allow from all
	 </Proxy>
	 ProxyPreserveHost on
	 ProxyPass / http://localhost:8080/ nocanon
	 AllowEncodedSlashes NoDecode
	</VirtualHost>
```

* #### Start Jenkins
	
```
	$ sudo a2ensite jenkins
	$ sudo apache2ctl restart
	$ sudo /etc/init.d/jenkins start
```
	
-------
	
## Config Jenkins
1. Open the Jenkins Server from browser with http://[SERVER_IP]:8080/
2. Input initial Admin Password (Get it from the VM)
3. Install suggested plugins ... wait ...
4. When Jenkins page show 

	Choose **System Management** -> **Plugins Management**

	Check following plugins are installed, most of them are already installed in step 3.
	
	* git client plugin 
	* git plugin 
	* Gradel plugin
	* Bitbucket
	* Bitbucket Build Status Notifier Plugin
	* Bitbucket Pullrequest Builder Plugin
	* Android Lint Plugin*
	* Android Emulator Plugin*
	* Google Cloud Messaging Notification Plugin*
	* Google Play Android Publisher Plugin*

	> \* - *optional plugins*

	Choose **System Management** -> **Global Tool Configuration**

	* Gradle Installation -> auto install
	* Git Installation -> auto install
	* JDK Installation -> auto install (need oracle account/pwd)
	* Maven Installation -> auto install
	
	Choose **Credentials** -> **System** -> **Global credentials (unrestricted)** -> **Add Credentials**
	
	* Choose **Kind** -> ```SSH Username with private key```
	* Generate a SSH key pairs, put private key here and public key in **Bitbucket Settings** -> **SSH keys**
	* You may directly input the private key or a file under Jenkins Server's path.

--------
	
##Install Android tools on VM

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

	$ cd /opt/android-sdk-linux/tools/
List all the sdk tools

	$ android list sdk --all

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

	$ sudo apt-get install git-core

* #### Install Oracle JDK 8

	$ sudo add-apt-repository ppa:webupd8team/java
	$ sudo apt-get update
	$ sudo apt-get install oracle-java8-installer
	
* #### Install 32bit compatible libs for Anroid emulator

	$ sudo apt-get install -y libc6-i386 lib32stdc++6 lib32gcc1 lib32ncurses5 lib32z1

	
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

## Setup Jenkins Build Job
1. Open Jenkins in Browser with url  **https://[SERVER_IP]:8080/**
2. Create a new task with 'free style project'
3. In **Build trigger**
	* Check 'Build when a change is pushed to BitBucket' 
4. In **Source Code Management**
	* Input the Git url in 'Repository URL'
	* Select the private key you've input 
	* Input the branches for build
5. In **Build**
	* Select 'Invoke Gradle script'
	* Select 'Use Gradle Wrapper'
	* Check 'From Root Build Script Dir'
	* Input '*clean build* ' in 'Switches'

------

## Settings for Jenkins in Bitbucket

* Config Jenkins build for every code push to specified branches.

	In Bitbucket Repository's **Settings** -> **Webhooks**
 
	* Add new webhook 'URL' -> http://[SERVER_IP]:8080/bitbucket-hook/
	* Check the 'Skip certificate verfication' 
	* Save the new webhook

* Config [Bitbucket Cloud Build Status Notifier](https://wiki.jenkins-ci.org/display/JENKINS/Bitbucket+Cloud+Build+Status+Notifier+Plugin)

* Config [Bitbucket Pull Requester Plugin](https://github.com/nishio-dens/bitbucket-pullrequest-builder-plugin)

> Pull Request plugin is not actually working in my test

##Add Android Emulator for Jenkins to run AndroidUnitTest

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



##Done
New push to the Bitbucket Repo will trigger a Jenkins build now.

*Note*:
> 
> First time build may have error like
> 
> "A problem occurred configuring project ':app'.
> Failed to install the following SDK components:
> 
>   [Solver for ConstraintLayout 1.0.0-beta4, ConstraintLayout for Android 1.0.0-beta4]
  Please install the missing components using the SDK manager in Android Studio."
>
> No Actions needs to do, try again, it will be installed automatically.




