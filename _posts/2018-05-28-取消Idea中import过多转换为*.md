---
layout: post  
title: 取消Idea中的import *
categories: [ide, idea]  
description:  
keywords: idea,  import
---

#### idea的默认设置中, 如果import相同包下的文件过多, 会出现import xxx.xxx.xxx.* 的情况, 这里记录下如何取消这种设置 :
#### Idea -> Preferences -> Editor -> Code Style -> Java  
#### 在右侧的import标签下
```
设置Class count to use import with '*' 为 999
设置Names count to use static import with '*' 为 999
```


![](https://taojintianxia.github.io/images/posts/ide/idea/arrange_import.png)