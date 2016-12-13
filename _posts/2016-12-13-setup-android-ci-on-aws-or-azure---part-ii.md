---
layout: post
title: "Setup Android CI on AWS or Azure   part II"
description: ""
category: 
tags: []
---

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
