---
layout: post  
title: Sub-process /usr/bin/dpkg returned an error code (1)  
categories: [docker, 无法启动]  
description: Sub-process /usr/bin/dpkg returned an error code (1)  
keywords: Sub-process, docker, dpkg  
---

### 今天无意中卸载了一个前几天装的 docker-compose 工具，然后就发现docker 无法启动了，然后就开始了找问题的过程：

1. 执行命令 docker ps -a, 提示 docker 无法启动，请检查 docker daemon

2. 那就尝试启动 docker 吧：  

```
sudo systemctl daemon-reload && sudo systemctl restart docker
```
得到提示 : Sub-process /usr/bin/dpkg returned an error code (1)  

3. 这么顽固？那我直接删了重装行不？

```
sudo apt-get remove docker docker-engine docker.io containerd runc

sudo apt-get install docker-ce docker-ce-cli containerd.io
```

还没执行完第一条语句，就提示Sub-process /usr/bin/dpkg returned an error code (1) 

4. 按照如下方式试试吧：

```
## 1. try to reconfigure the package database. Probably the database got corrupted while installing a package
sudo dpkg --configure -a

## 2. If a package installation was interrupted previously, you may try to do a force install.
sudo apt-get install -f

## 3. search for the files for docker
ls -l /var/lib/dpkg/info | grep -i docker

## 4. delete the corrupted files
sudo mv /var/lib/dpkg/info/docker-ce* /tmp
```

5. 启动 docker，完事