---
layout: post  
title: 浅析 ShardingSphere中的 sql-test  
categories: [ss, test]  
description: 浅析 ShardingSphere中的 sql-test  
keywords: ss, test  
---

### 简介
sharding-sql-test 是一个新抽取出的模块，主要用于 SQL 解析后的断言验证。在ss 中，sql 解析是个比较复杂的部分，在非常谨慎的情况下做的任何修改，也无法保证解析后的正确性，针对解析的断言，是质量保证的门槛。  

### 加载
sql-test 模块下，有一个 SQLCasesLoader 类，该类会按照根据指定的参数，加载 resources 下对应目录中的 xml 文件。

xml 文件的格式如下 : 

```xml
<sql-cases db-types="MySQL">
    <sql-case id="delete_with_sharding_value" value="DELETE FROM t_order WHERE 
    <sql-case id="select_sum" value="SELECT SUM(user_id) AS user_id_sum FROM t_order" />
</sql-cases>
```

这里我们只需要填写一个 sql case id，以及我们想要去断言的 sql 即可。SQLCasesLoader 会将这些信息加载到一个 map 中（Map\<String, SQLCase> sqlCases）
