---
layout: post
title: "Android Interview Questions"
description: ""
category: 
tags: [Android, interview]
---
### Parcable vs Serializable

Parcable has better performance than Serializable, but it is not consistant between different version of Android, so it’s good to use parcable between android app’s internal components, ALDI and intent, bundle. Serializable is more suitable for storing data consistantly in files or transferring data between apps and server.
