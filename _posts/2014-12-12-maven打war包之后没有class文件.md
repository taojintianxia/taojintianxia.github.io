---
layout: post  
title: maven打war包之后没有class文件  
categories: [maven]  
description:    
keywords: maven    
---

#### 用maven打了war包之后部署到tomcat下居然无法执行,看了一下原来没有任何编译的.class文件.

#### 查了一下,是自己手欠把source的src改成src.main.java之类的目录了,但是没有在maven中制定位置

#### 将maven的build下的<sourceDirectory>src.main.java</sourceDirectory>设置一下就好了

