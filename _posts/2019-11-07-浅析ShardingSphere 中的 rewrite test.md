---
layout: post  
title: 浅析ShardingSphere 中的 rewrite test  
categories: [shardingsphere, rewrite, test]  
description: 浅析ShardingSphere 中的 rewrite test  
keywords: shardingsphere, rewrite, test  
---

### 前言

前面我们分别简单了解了一下 ShardingSphere 中的 sql parse 以及 integration test，今天我们看看 rewrite 的 test

面向逻辑库与逻辑表书写的SQL，并不能够直接在真实的数据库中执行，SQL改写用于将逻辑SQL改写为在真实数据库中可以正确执行的SQL。 它包括正确性改写和优化改写两部分。接下来我们就基于这些点，去看看 rewrite 的测试吧

### 测试

rewrite 的测试用例在 `sharding-core/sharding-core-rewrite` 的 test中。

