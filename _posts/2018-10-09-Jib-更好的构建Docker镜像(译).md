---
layout: post  
title: Jib-更好的构建Docker镜像  
categories: [Jib, Docker]  
description:   
keywords: Jib, Docker  
---

## Jib - 更好的构建Docker镜像  
#### 本文翻译自 [google blog](https://cloud.google.com/blog/products/gcp/introducing-jib-build-java-docker-images-better) : Introducing Jib — build Java Docker images better  
#### 容器技术使得Java开发人员比以往更加接近"一次编写, 到处运行"的目标, 但是将一个Java应用容器化并不是一件简单的事情 : 你得写一个Dockerfile, 以root的身份后台运行docker, 等待构建完成, 最终将该image推送到仓库中去. 并非所有的Java开发人员都是容器专家, 构建一个jar文件要怎么搞起...  

#### 为了应对这一挑战, 我们很高兴向您介绍[Jib](https://github.com/GoogleContainerTools/jib), 一款来自google的Java开源容器化工具, 本工具可以让那些Java开发人员用自己熟悉的Java工具来构建镜像. Jib可以快速而又简单的处理一个应用打包到镜像中的所有事宜, 此过程中并不需要你编写任何Dockerfile或者在本地安装docker, 它直接集成在maven或者gradle中, 使用时只需要将插件添加到您的构建中, 您就可以立即将Java应用容器化.  
Docker的构建流程 :  
![](https://taojintianxia.github.io/images/posts/jib/docker_build_flow.png)  
Jib的构建流程 :  
![](https://taojintianxia.github.io/images/posts/jib/jib_build_flow.png)  

### Jib是如何让开发变得更加容易的 :  
 