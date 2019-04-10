---
layout: 开启docker daemon服务  
title: 开启docker daemon服务  
categories: [docker, daemon]  
description: 开启docker daemon服务  
keywords: docker, deamon  
---

### 前言
#### 因为最近写了一个自动构建工具, 用到了docker的一些Java API, java通过API调用docker, 是需要有docker daemon服务的, 这里记录一下docker daemon服务的开启方式

## 开启服务
#### 1. 安装docker
```
## 切记不能在docker容器中安装docker
## 而且官方提供的docker in docker的dind方式也没什么可用性
## 先老老实实找台机器装吧
yum install docker -y
```

#### 2. 创建配置文件
在/etc/docker/下创建daemon.json, 内容如下 : (这里默认使用2375这个端口, 可以自己指定) 
```json

{
   "insecure-registries":[
      "docker.xx.com",
      "docker.xx.com:50000"
   ],
   "hosts":[
      "tcp://0.0.0.0:2375",
      "unix:///var/run/docker.sock"
   ]
}
```

#### 3. 使服务生效
```bash
systemctl daemon-reload
systemctl restart docker.service
```
