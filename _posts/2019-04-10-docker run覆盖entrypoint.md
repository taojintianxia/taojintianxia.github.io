---
layout: post  
title: docker run覆盖entrypoint  
categories: [docker, entrypoint]  
description: docker run覆盖entrypoint  
keywords: docker, entrypoint  
---

#### 最近总是遇到一些镜像, docker run之后一闪而退, 一些是执行了一次性的任务, 这种可以通过如下方式去解决
```
## 先把一台机器hang住
docker run -it -name abc --rm xxx/xxximage /bin/sh -c "sleep 999"
## 然后另一台机器登录去看状态
docker exec -it abc bash
```

#### 但是还有些情况是, image本身就有问题, 例如ADD的时候没有将指定sh加入镜像, entrypoint执行的时候就直接报错, 甚至连sleep的机会都没有,docker logs也是看不到问题

#### 这时候可以选择覆盖entrypoint, 然后进入容器再看具体执行情况
```
## 先记录下entrypoint
docker inspect xxx/xxximage
## 覆盖掉默认entrypoint
docker run --entrypoint "" --rm -it xxx/xxximage bash
## 这时候就进入容器了, 可以试着执行entrypoint看看有什么效果
```
