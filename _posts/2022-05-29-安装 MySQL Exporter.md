---
layout: post  
title: 安装 MySQL Exporter  
categories: [mysql_exporter, exporter, 安装]  
description: 安装 MySQL Exporter  
keywords: mysql_exporter, exporter, 安装  
---

## 前言
Prometheus 的 expoter 会收集对应目标的一些信息，然后将其以拉取的方式提供给 Prometheus

目前除了常用的 node_exporter 之外，我们也需要观察 MySQL 的内部统计信息。这时候可以使用 Prometheus 官方提供的 MySQL Exporter 来处理。

## 安装
确保主机上已经安装了 MySQL



String DROP_TABLE = "DROP TABLE IF EXISTS sbtest1";

String CREATE_TABLE = "CREATE TABLE sbtest1 (\n" +
            "id bigint NOT NULL,\n" +
            "k int NOT NULL default '0',\n" +
            "c char(120) NOT NULL default '',\n" +
            "pad char(60) NOT NULL default '',\n" +
            "PRIMARY KEY (id)\n" +
            ");";
    
String CREATE_INDEX = "CREATE INDEX k_1 ON sbtest1(k);";
    
String INSERT_DATA = "INSERT INTO sbtest1 (k, c, pad) VALUES (1000, 'random-char-1', 'random-char-pad-1000');";

String INSERT_DATA_2 = "INSERT INTO sbtest1 (k, c, pad) VALUES (2000, 'random-char-1', 'random-char-pad-1000');";
    
String UPDATE_DATA = "UPDATE sbtest1 SET c='UPDATED CHAR' WHERE k=1000;";

String DELETE_DATA = "DELETE FROM sbtest1 WHERE k=1000;";
    
String SELECT_DATA = "SELECT k, c , pad from sbtest1";