---
layout: post  
title: Can not open gc.log due to Permission denied  
categories: [Permission denied, apollo]  
description: Can not open gc.log due to Permission denied  
keywords: Permission denied, apollo  
---

今天在 ubuntu 上安装 Apollo 时候遇到的问题，然后 google 了一下，发现很多工具安装的时候都会遇到这个问题，简单的 chmod -R 755 执行文件是没用的，应该按照如下方式设置：

```
sudo chmod -R 755 /opt/logs/
sudo chmod -R 755 /xx/apolo/

sudo chown -R $USER:$GROUP /opt/logs/
sudo chown -R $USER:$GROUP /xx/apollo/
```