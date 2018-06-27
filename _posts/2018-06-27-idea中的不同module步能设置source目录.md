---
layout: post  
title: idea中的不同module下不能设置source目录  
categories: [idea, ide]  
description:  
keywords: idea, ide   
---

##### 今天遇到个问题, 就是在idea的项目下, 建立不同的module, 第二个module居然不能设置source/resource/test的目录.具体表现为, 右键module选Module Settings, 然后将java设置为source目录, 这时候会提示 : two modules in a project cannot share the same content root  

##### 解决办法 : 将项目根目录下的src目录直接删了即可.