---
layout: post  
title: Windows机器上上传文件到Linux  
categories: [windows, pscp]  
description:   
keywords: windows, pscp  
---

#### 1.在putty的网站上下载putty跟pscp

#### 2.安装ssh跟putty
```
sudo apt-get install openssh-server

sudo apt-get install putty-tools
```
#### 3.在windows下启动cmd , 在cmd下调用pscp传输文件到linux , 例如 : pscp c:/test.txt kane@192.168.96.136:/home/kane/download/

这里切记download后面的斜线

