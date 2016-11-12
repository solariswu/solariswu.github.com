---
layout: post
title: "leetcode 116. Populating Next Right Pointers"
description: ""
category: 
tags: [algrithem, leetcode]
---

```
         1                        1 -> NULL
       /  \                     /  \ 
      2    3        =>         2 -> 3 -> NULL
     / \  / \                 / \  / \
    4  5  6  7               4->5->6->7 -> NULL
```

```c++
        while (p && p->left) {
            p->left->next = p->right;
            p->right->next = NULL;
    
            if (p->next) {    
                p->right->next = p->next->left;
            }
            p = p->next;
        }
```
