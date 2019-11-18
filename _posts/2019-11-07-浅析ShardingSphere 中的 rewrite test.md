---
layout: post  
title: 浅析ShardingSphere 中的 rewrite test  
categories: [shardingsphere, rewrite, test]  
description: 浅析ShardingSphere 中的 rewrite test  
keywords: shardingsphere, rewrite, test  
---

### 前言

前面我们分别简单了解了一下 ShardingSphere 中的 sql parse 以及 integration test，今天我们看看 rewrite 的 test

面向逻辑库与逻辑表书写的SQL，并不能够直接在真实的数据库中执行，SQL改写用于将逻辑SQL改写为在真实数据库中可以正确执行的SQL。 它包括正确性改写和优化改写两部分，所以 rewrite 的测试都是基于这些改写方向进行校验的。

### 测试

rewrite 的测试用例位于 `sharding-core/sharding-core-rewrite` 下的 test 中。rewrite 的测试主要依赖如下几个部分配置：

  - 测试引擎
  - 环境配置
  - 验证数据

测试引擎就是 rewrite 测试的入口，跟其他引擎有一样，都用到到了 Junit 的 `Parameterized` annotation，引擎会将 `test\resources`目录中测试类型下对应的 xml 文件读取，然后按读取顺序一一进行验证。

环境配置存放在 `test\resources\yaml` 路径中测试类型下对应的 yaml 中。配置了dataSources，shardingRule，encryptRule 等信息，默认使用的是 H2 内存数据库

验证数据存放在 `test\resources` 路径中测试类型下对应的 xml 文件中，文件中保存了要读取的配置文件位置，要测试的 SQL，参数，以及期待的结果，例如：

```
<rewrite-assertions yaml-rule="yaml/sharding/sharding-rule.yaml">
    <rewrite-assertion id="insert_values_with_columns_with_id_for_parameters">
        <input sql="INSERT INTO t_account (account_id, amount, status) VALUES (?, ?, ?) ON DUPLICATE KEY UPDATE amount = VALUES(amount)" parameters="100, 1000, OK" />
        <output sql="INSERT INTO t_account_0 (account_id, amount, status) VALUES (?, ?, ?) ON DUPLICATE KEY UPDATE amount = VALUES(amount)" parameters="100, 1000, OK" />
    </rewrite-assertion>
    
    <rewrite-assertion id="insert_values_with_columns_with_id_for_literals" db-type="MySQL">
        <input sql="INSERT INTO t_account (account_id, amount, status) VALUES (100, 1000, 'OK') ON DUPLICATE KEY UPDATE amount = VALUES(amount)" />
        <output sql="INSERT INTO t_account_0 (account_id, amount, status) VALUES (100, 1000, 'OK') ON DUPLICATE KEY UPDATE amount = VALUES(amount)" />
    </rewrite-assertion>
</rewrite-assertions>
```
我们只需要在 xml 文件中编写测试数据，配置好配置文件，就可以在不更改任何 Java 代码的情况下校验对应的 SQL 了。
