---
layout: post  
title: Jib-更好的构建Docker镜像  
categories: [Jib, Docker]  
description:   
keywords: Jib, Docker  
---

Jib - 更好的构建Docker镜像  
本文翻译自 [google blog](https://cloud.google.com/blog/products/gcp/introducing-jib-build-java-docker-images-better) : Introducing Jib — build Java Docker images better  
容器技术使得Java开发人员比以往更加接近"一次编写, 到处运行"的目标, 但是将一个Java程序容器化并不是一件简单的事情 : 你得写一个Dockerfile, 以root的身份后台运行docker, 等待构建完成, 最终将该image推送到仓库中去. 并非所有的Java开发人员都是容器专家, 构建一个jar文件要怎么搞起...  

