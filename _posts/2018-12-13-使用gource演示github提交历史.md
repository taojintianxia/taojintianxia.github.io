---
layout: post  
title: 基于Gource动态展示Github提交历史  
categories: [gource, github]  
description: 基于Gource动态展示Github提交历史  
keywords: gource, github  
---

## 最近发现一款有趣的工具, 可以以动画的形式动态的展示一个git项目的提交历史, 本文记录一下相关使用方式

### 1. 安装gource
```
## brew是Mac下的包管理工具, 如果是其他操作系统, 自行搜索安装方式.
## 先安装FFMPEG
brew install ffmpeg

## 安装gource
brew install gource
```

### 2. 跑起来个例子
```
## 进入git项目的根目录, 执行如下命令
gource --hide dirnames,filenames --seconds-per-day 0.1 --auto-skip-seconds 1 -1280x720 -o - | ffmpeg -y -r 60 -f image2pipe -vcodec ppm -i - -vcodec libx264 -preset ultrafast -pix_fmt yuv420p -crf 1 -threads 0 -bf 0 gource.mp4
```

### 3. 记录下一些常用的参数吧 :
  - 1. --camera-mode : 镜头模式, 有两个参数
    - overview
    - track