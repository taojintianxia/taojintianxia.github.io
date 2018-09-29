---
layout: post  
title: 通过docker快速拉起mysql  
categories: [docker, mysql]  
description: 通过docker快速拉起mysql  
keywords: docker, mysql  
---

最近需要调研一下sharding-sphere, 如果涉及到分库分表, 以及不同shard的算法, 需要搭建几个不同的数据库, 实在是懒得在本机上装多个数据库, 于是通过如下命令用docker拉起几个数据库 :  
```
docker run -d -p 4406:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_ROOT_HOST=% --name mysqlA mysql/mysql-server:5.7
```
需要注意的是, mysql5.7如果不加上MYSQL_ROOT_HOST=%, 会导致无法访问mysql, 提示错误Host '172.21.0.1' is not allowed to connect to this MySQL server