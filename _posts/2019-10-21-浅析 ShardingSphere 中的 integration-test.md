---
layout: post  
title: 浅析 ShardingSphere 中的 integration-test.  
categories: [ss, test]  
description: 浅析 ShardingSphere 中的 integration-test.  
keywords: ss, test    
---

### 简介

前面我们简单的了解了一下 ss 中的 sql-test 模块，今天我们看看 integration-test 模块。integration-test 知名见意，就是集成测试引擎，在这个引擎中，可以有针对性的对dcl，dml，dql 等 SQL 在不同的数据库下，执行分表，分库，主从等等的集成测试，而不像前面说的 sql-test，仅仅针对某个功能点进行单独的测试了。

### 配置文件

不同于其他断言，集成测试需要一个真实的测试环境(我们可以安装相应的数据库，不过更推荐 docker 的方式)，我们需要在 resource/integrate 下的env.properties 中进行配置 :

```
# 针对特殊情况测试的开关
run.additional.cases=false

# sharding 的策略，可以指定多种策略
sharding.rule.type=db,tbl,dbtbl_with_masterslave,masterslave

# 指定要测试的数据库，可以指定多种数据库
# databases=H2,MySQL,Oracle,SQLServer,PostgreSQL
databases=MySQL

# mysql 的配置
mysql.host=127.0.0.1
mysql.port=13306
mysql.username=root
mysql.password=root

## postgresql 的配置
postgresql.host=db.psql
postgresql.port=5432
postgresql.username=postgres
postgresql.password=

## sqlserver 的配置
sqlserver.host=db.mssql
sqlserver.port=1433
sqlserver.username=sa
sqlserver.password=Jdbc1234

## oracle 的配置
oracle.host=db.oracle
oracle.port=1521
oracle.username=jdbc
oracle.password=jdbc
```