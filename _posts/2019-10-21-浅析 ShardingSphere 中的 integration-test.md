---
layout: post  
title: 浅析 ShardingSphere 中的 integration-test.  
categories: [ss, test]  
description: 浅析 ShardingSphere 中的 integration-test.  
keywords: ss, test    
---

### 简介

前面我们简单的了解了一下 ss 中的 sql-test 模块，今天我们看看 integration-test 模块。integration-test 知名见意，就是集成测试引擎，在这个引擎中，可以有针对性的对dcl，dml，dql 等 SQL 在不同的数据库下，执行分表，分库，主从等等的集成测试，而不像前面说的 sql-test，仅仅针对某个功能点进行单独的测试了。

### 