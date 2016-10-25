---
layout: post
title: "About SharedPreference"
description: ""
category: 
tags: [Android, sharedpreference]
---
* Don't put large data into it, eg. >50K.
* Don't store JSON data into SP. Directly use Json format to store into file.
* Don't use multi sp.edit.apply() or commit() in UI thread.
* Don't use SP for cross threads usage, don't trust MODE_MULTI_PROCESS flag. Use ContentProvider instead.
* Use different SP to store those frequently changed value and the others.
* Don't put non-related items into one SP and never put them into the Default SP.
