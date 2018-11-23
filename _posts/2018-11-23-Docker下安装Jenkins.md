---
layout: post  
title: 在Docker下安装Jenkins  
categories: [Docker, Jenkins]  
description: 在Docker下安装Jenkins  
keywords: Docker, Jenkins  
---

## Docker下安装Jenkins

#### 访问官方的网站可以获得相关的教程以及说明 : https://github.com/michaelneale/jenkins-ci.org-docker

#### 在Docker中安装Jenkins, 前提是机器上已经安装了Docker, 如果没有安装, 还需要先装一下docker, 具体安装办法这里就不说了.

#### 装好docker, 执行如下命令 : 
```
## your_jenkins_home改为你自己定义的宿主机目录
docker run -d -v your_jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

#### 稍等片刻, docker就会下载并启动Jenkins, 这时候访问http://localhost:8080/, 可以看到如下界面 :  

![](https://taojintianxia.github.io/images/posts/docker/Docker_jenkins_1.jpg) 

#### 这时候有两种办法读取相关密码 :
- 1. 通过docker logs CONTAINER的办法, 用docker logs命令查看相关容器, 可以看到密码
- 2. 直接查看密码文件, 例如: cat your_jenkins_home/secrets/initialAdminPassword查看相关密码

#### 
