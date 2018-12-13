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
    - overview : 全局查看提交状态
    - track : 跟踪每一个commiter的提交状态
  - 2. -s, --seconds-per-day
    - 模拟速度, 一天在视频里占据几秒.可以设置为0.1
  - 3. --hide
    - 在视频中隐藏信息, 可以隐藏时间, 文件名, 目录名等
  - 4. --start-date "YYYY-MM-DD hh:mm:ss +tz"
    - 统计项目的开始时间, 格式可以自定义. 同样有--stop-date "YYYY-MM-DD hh:mm:ss +tz"作为结束时间的参数
  - 5. --logo IMAGE
    -  设置logo
  - 6. --date-format FORMAT
    - 设置时间格式, google一下strftime format的使用方式就知道了.


