---
layout: post  
title: 基于 docker 的 mysql 主从同步  
categories: [docker, mysql, 主从]  
description: 基于 docker 的 mysql 主从同步  
keywords: docker, mysql, 主从  
---

#### 因为 shardingsphere 中有用到主从的环境，在虚机或者自己的电脑上创建 mysql 又过于麻烦，于是就有了这篇文章 

#### Master 的设置
##### 1. 首先我们启动 master 的容器：

```
docker run --name mysql-master -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -p13306:3306 -d mysql:5.7
```
这时候一个没有密码的 mysql 容器就被启动起来了， 我们进入容器

```
docker exec -it mysql-master bash
```

##### 2. 创建my.cnf
这里的 mysql 没有 my.cnf，我们需要/etc/下面创建一个（官方的镜像是支持使用容器的环境参数来控制 mysql 的，而不必创建 my.cnf.这是个优化点，稍后调研后会补完）

```
## 因为实在是不习惯使用其他编辑器，我直接装了个 vim
apt-get update && apt-get install vim -y
vim /etc/my.cnf
```
内容填入如下信息即可, 这里我们要注意的是，server-id 要填一个集群内唯一的数字，这里就设定为 master 为 1，其他的 slave 就继续累加，slave-1为 2，slave-2 为 3，以此类推
```
[mysqld]
pid-file=/var/run/mysqld/mysqld.pid
socket=/var/run/mysqld/mysqld.sock
datadir=/var/lib/mysql
#log-error=/var/log/mysql/error.log
log-bin=/var/log/mysql/mysql-bin.index
server-id=1
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
```

##### 3. 创建同步用户
运行 mysql 命令，进入 mysql，执行如下 sql：

```
## 创建用户
CREATE USER 'mysql_replication_user'@'%' IDENTIFIED BY 'mysql_replication_password'; 

## 赋予权限
GRANT SELECT, REPLICATION SLAVE ON *.* TO 'mysql_replication_user'@'%';
```

常规操作的话，目前 master 上的目前已经基本结束了，但是我们用的是 docker，没法重启 mysql 服务，所以我们退出当前的容器，重启下容器

```
docker restart mysql-master
```

再次进入 mysql-master 并启动 mysql查看状态：

```
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      154 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

我们可以看到，此时 mysql master 的状态已经出现了

##### 4. 启动 slave 容器
