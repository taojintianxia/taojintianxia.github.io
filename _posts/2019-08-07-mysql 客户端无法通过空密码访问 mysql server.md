---
layout: post  
title: mysql 客户端无法通过空密码访问 mysql server  
categories: [docker, mysql, 空密码]  
description: mysql 客户端无法通过空密码访问 mysql server  
keywords: docker, mysql, 空密码  
---

#### 今天遇到个奇怪的问题，在没搞清楚原理的情况下，只好先记录下现象。

#### mysql 无法通过 localhost 访问空密码的（MYSQL_ALLOW_EMPTY_PASSWORD=yes） mysql server

我们通过 docker 启动一个包含空密码的 mysql server 

```bash
docker run --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -p13306:3306 -d mysql:5.7
```
然后我们通过客户端访问

```
$ mysql -hlocalhost -uroot -P13306
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
```

我们可以看到，通过客户端访问 docker 中无密码的 server，是不行的，可是我们是可以通过 127.0.0.1 进行访问

```
$ mysql -h127.0.0.1 -uroot -P13306
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.25 MySQL Community Server (GPL)
```

同样，如果我们通过 JDBC 访问 mysql，DataSource 填127.0.0.1 或者 localhost，都是无法访问 mysql server 的

如果我们通过 docker 启动一个有密码的 mysql server

```bash
docker run --name mysql -e MYSQL_ROOT_PASSWORD=root -p13306:3306 -d mysql:5.7
```
这时候无论访问 localhost，还是 127.0.0.1 就都是可以的了

```bash
$ mysql -hlocalhost -uroot -P13306 -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 77
Server version: 5.7.24 MySQL Community Server (GPL)
```

```bash
$ mysql -h127.0.0.1 -uroot -P13306 -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.25 MySQL Community Server (GPL)
```

目前搞不清楚是什么情况，但是在弄懂之前，还是使用有密码的设置吧，否则容易跳到坑里