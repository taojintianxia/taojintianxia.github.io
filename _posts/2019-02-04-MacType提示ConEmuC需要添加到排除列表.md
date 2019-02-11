---
layout: post  
title: MacType提示ConEmuC需要添加到排除列表  
categories: [MacType, ConEmuC, Windows]  
description: MacType提示ConEmuC需要添加到排除列表  
keywords: MacType, ConEmuC, Windows  
---

#### 最近在windows上安装了MacType, 显示效果确实有一定的改善, 但是使用ConEmuC的时候, 遇到了如下的问题 : 
![](https://taojintianxia.github.io/images/posts/mactype/ConEmu64_AGkkIOdVwE.png)  

#### 按照官方的FAQ设置MacType为独立启动模式并不能解决问题, 只好将ConEmuC添加到exclude list了 :  
  - 1.右键任务栏的MacType, 选择打开配置文件 -> 用记事本打开
  - 2.找到UnloadDll
  - 3.在其下添加相关进程

```
[UnloadDll]
ConEmuC.exe
ConEmuC64.exe
```