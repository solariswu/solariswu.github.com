---
layout: post
title: "Setup Android CI on AWS or Azure - part 1"
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
