---
layout: post  
title: CentOS7下安装Docker  
categories: [docker, centos7]  
description: some word here  
keywords: docker, centos7  
---

## 仅此记录一下centos7下docker的安装过程.

### 首先删除残存的docker
```
yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-selinux \
docker-engine-selinux \
docker-engine

sudo rm -rf /var/lib/docker

## 可能还是无法删除, 继续下面的这一步 :
## 先获取安装列表, 可能是这样的 : 
## docker-engine.x86_64  17.03.0.ce-1.el7.centos  @dockerrepo
yum list installed | grep docker
yum remove -y docker-engine.x86_64
```

### 安装相关依赖
### 1. 安装依赖包
```
sudo yum install -y yum-utils \
device-mapper-persistent-data \
lvm2
```

### 2. 使用下面的命令创建一个稳定的仓库
```
sudo yum-config-manager \
--add-repo \
https://download.docker.com/linux/centos/docker-ce.repo
```

### 3. 选装 : 	启用edge跟test库, 这些库包含在上面的docker.repo文件中，但默认情况下处于禁用状态。您可以将它们与稳定存储库一起启用。
```
sudo yum-config-manager --enable docker-ce-edge
sudo yum-config-manager --enable docker-ce-test

## 可以根据下面的命令禁用test跟edge
sudo yum-config-manager --disable docker-ce-edge
```

### 4. 下面进入正题, 安装Docker :  
```
yum install -y docker-ce

## 如果想安装指定版本的docker, 执行如下 :
## 获取相关的docker版本
yum list docker-ce --showduplicates | sort -r

## 上一步会得到类似这样的版本号 :
## docker-ce.x86_64    18.09.0.ce-1.el7.centos    docker-ce-stable
sudo yum install docker-ce-<VERSION STRING>
```

### 5. 启动docker
```
sudo systemctl start docker
## 启动后可以执行个命令看看是否启动了, 例如 :
docker images
```

### 实际上, 上面的安装过程, 可以一句话就搞定 :  
```
curl -fsSL https://get.docker.com/ | sh
sudo systemctl start docker
```