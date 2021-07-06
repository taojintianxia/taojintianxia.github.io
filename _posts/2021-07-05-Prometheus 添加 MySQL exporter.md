---
layout: post  
title: Prometheus 添加 MySQL exporter  
categories: [prometheus, mysql, exporter, mysql_exporter]  
description: Prometheus 添加 MySQL exporter  
keywords: prometheus, mysql, exporter, mysql_exporter  
---


## 为 Prometheus 添加 MySQL exporter

MySQL exporter 为 Prometheus 官方提供的用于 MySQL 数据库的 exporter，通过访问  `performance_schema` 跟 `information_schema` 两个 schema 下的相关表，以及一些其他系统信息，提供了对 MySQL 的基础监控数据。

这里我们记录一下如何安装 mysql_exporter，留用将来安装时使用


## 安装&配置

首先我们下载 mysql_exporter。在 mysql_exporter 的 [github release 页面](https://github.com/prometheus/mysqld_exporter/releases) 选择一个适合我们机器的版本，这里我选择了 linux amd64 并下载

```
wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.13.0/mysqld_exporter-0.13.0.linux-amd64.tar.gz
tar -zxvf mysqld_exporter-0.13.0.linux-amd64.tar.gz
mkdir -p /opt/sphereEx/prometheus_exporter
mv mysqld_exporter-0.13.0.linux-amd64/mysqld_exporter /opt/sphereEx/prometheus_exporter/
chmod +x /opt/sphereEx/prometheus_exporter/mysqld_exporter
```

创建访问数据的用户（可选）：

这并不是必须的操作，如果我们不愿意用 root 用户，可以创建一个导出数据的用户 mysql_exporter。
这里设置 `MAX_USER_CONNECTIONS` 为 2 的目的是防止该用户有过多请求影响 MySQL 的正常使用

```
CREATE USER 'mysqld_exporter'@'localhost' IDENTIFIED BY 'Abcd@1234' WITH MAX_USER_CONNECTIONS 2;
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'mysqld_exporter'@'localhost';
FLUSH PRIVILEGES;
```

接下来创建 mysql_exporter 的配置文件

vim /etc/.mysqld_exporter.cnf

内容填写如下：

```
[client]
user= mysqld_exporter
password=Abcd@1234
port=3306
host=127.0.0.1
```

目前 mysql_exporter 的安装跟配置就已经完成了，接下来我们将其启动

## 启动

如果仅仅是命令行启动，无法保证 mysql_exporter 的可用性，这里我们通过 systemd 的方式来启动。
首先创建 systemd 需要的启动脚本

vim /etc/systemd/system/mysql_exporter.service

内容如下：

```
[Unit]
Description=Prometheus MySQL Exporter
After=network.target

[Service]
User=root
Group=root
Type=simple
Restart=always
ExecStart=/opt/sphere-ex/prometheus_exporter/mysqld_exporter \
--config.my-cnf /etc/.mysqld_exporter.cnf \
--collect.global_status \
--collect.info_schema.innodb_metrics \
--collect.auto_increment.columns \
--collect.info_schema.processlist \
--collect.binlog_size \
--collect.info_schema.tablestats \
--collect.global_variables \
--collect.info_schema.query_response_time \
--collect.info_schema.userstats \
--collect.info_schema.tables \
--collect.perf_schema.tablelocks \
--collect.perf_schema.file_events \
--collect.perf_schema.eventswaits \
--collect.perf_schema.indexiowaits \
--collect.perf_schema.tableiowaits \
--collect.slave_status \
--web.listen-address=0.0.0.0:9104

[Install]
WantedBy=multi-user.target

```

启动 mysql_exporter：

```
systemctl daemon-reload
systemctl enable mysql_exporter
systemctl start mysql_exporter
# 查看 mysql_exporter 状态
systemctl status -l mysql_exporter
```