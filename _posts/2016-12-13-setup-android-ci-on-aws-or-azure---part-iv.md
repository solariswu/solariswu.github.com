---
layout: post
title: "Setup Android CI on AWS or Azure - part 4"
description: ""
category: 
tags: []
---

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

-------

