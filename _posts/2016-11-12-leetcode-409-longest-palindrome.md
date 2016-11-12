---
layout: post
title: "leetcode 409 Longest Palindrome"
description: ""
category: 
tags: []
---

`
Input:
"abccccdd"

Output:
7

Explanation:
One longest palindrome that can be built is "dccaccd", whose length is 7.
`

```c++
    i = 0;
    while (0 != *p) {
        int offset = *p - 'A';
        if (0 == list[offset]) {
            list[offset] = 1;
            i ++;
        }
        else {
            list[offset] = 0;
            count +=2;
            i --;
        }
        p++;
    }

    if (i > 0) {
        count ++;
    }

    return count;
```
