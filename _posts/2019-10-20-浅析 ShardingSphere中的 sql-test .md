---
layout: post  
title: 浅析 ShardingSphere中的 sql-test  
categories: [ss, test]  
description: 浅析 ShardingSphere中的 sql-test  
keywords: ss, test  
---

### 简介
sharding-sql-test 是一个新抽取出的模块，主要用于 SQL 解析后的断言验证，ss 中，sql 解析是比较复杂的部分，在非常谨慎的情况下做的任何修改，也都不能保证解析后的正确性，针对解析的断言，是质量保证的门槛。  

### 加载
sql-test 模块下，有一个 SQLCasesLoader 类，该类会按照根据指定的参数，加载 resources 下对应目录中的 xml 文件。

