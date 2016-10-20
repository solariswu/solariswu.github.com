---
layout: post
title: "Android Fragment"
description: ""
category: 
tags: [android, UI, fragment, interview]
---

### Parcable vs Serializable
Parcable has better performance than Serializable, but it is not consistant between different version of Android, so it's good to use parcable between android app's internal components, ALDI and intent, bundle. Serializable is more suitable for storing data consistantly in files or transferring data between apps and server.

### How to keep and restore the user data while changing the screen landscape
* Activity - onSaveInstanceState / onRestoreInstanceState

```java
private boolean shouldSaveData = false;

private static final String KEY_SHOULD_SAVE_DATA = "save_data_key";

@Override
protected void onSaveInstanceState(Bundle outState) {
    outState.putBoolean(KEY_SHOULD_SAVE_DATA, shouldSaveData);
    super.onSaveInstanceState(outState);

    Log.i(TAG, "@@Activity onSaveInstanceState@@" + this.toString());
}

@Override
protected void onRestoreInstanceState(Bundle savedInstanceState) {
    super.onRestoreInstanceState(savedInstanceState);
    shouldSaveData = savedInstanceState.getBoolean(KEY_SHOULD_SAVE_DATA);
    Log.i(TAG, "@@Activity onRestoreInstanceState@@" + this.toString());
}

```

The lift cycle is `onCreate` –> `onStart` –> `onResume` –> `Running/landscape` –> `onPause` –> `onSaveInstanceState` –> `onStop` –> `onDestroy` –> `onCreate` –> `onStart` –> `onRestoreInstanceState` –> `onResume`; 

* Dialog - use DialogFragment  onSaveInstanceState / onActiviyCreated (restore)

* Fragment - setRetainInstance (true); then onCreate & onDestroy not called, the Fragment is kept during screen rotation.

### Pass parameter values between activity and fragments
* Use Callback
```java

((MainActivity) getActivity()).getParams();

```

* Use bundle and setArguments
```java

//activity：
Bundle args = new Bundle();
args.putInt("type",1);
accountRechargeListFragment.setArguments(args);
//fragment：
type = getArguments().getInt("type");

```

Ref:   <a href="http://www.gongmingqm10.net/blog/2015/12/16/you-should-know-about-android-rotate/"> you should know about android rotate </a>