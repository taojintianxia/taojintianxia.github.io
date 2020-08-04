---
layout: post  
title: ShardingSphere 中的规矩  
categories: [shardingsphere, ss, 规矩]  
description: ShardingSphere 中的规矩  
keywords: shardingsphere, ss, 规矩  
ahead: true  
recommend: true  
---

## ShardingSphere 中的规矩


### 1. indent 的问题

shardingsphere 中需要在每个空行以四个空格开始，在 idea 中是这样设置的：

Editor -> Code Style -> Java -> Keep indents in empty line

 ![](https://taojintianxia.github.io/images/posts/shardingsphere/idea/keep-indents-in-empty-line.png) 

### 2. 去掉 idea 保存时自动去除空格的限制

虽然前面已经让每个空行以四个空格为开头，但是保存的时候，idea 是会默认清除掉这个四个空格的。需要进行一下设置 :

Editor -> General -> Strip trailing space on save

 ![](https://taojintianxia.github.io/images/posts/shardingsphere/idea/strip-trailing.jpg) 
