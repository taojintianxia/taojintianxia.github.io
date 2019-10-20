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
sql-test 模块下，有一个 SQLCasesLoader 类，该类会按照根据指定的参数，递归加载 resources 下对应目录中的 xml 文件。

xml 文件的格式如下 : 

```xml
<sql-cases db-types="MySQL">
    <sql-case id="delete_with_sharding_value" value="DELETE FROM t_order WHERE order_id = ? AND user_id = ? AND status=?" >
        <table value = "t_order" />
        <columns>
            <column value = "order_id" />
            <column value = "user_id" />
            <column value = "statue" />
        </columns>
    </sql-case>
    <sql-case id="select_sum" value="SELECT SUM(user_id) AS user_id_sum FROM t_order" />
</sql-cases>
```

这里我们只需要填写一个 sql case id，以及我们想要去断言的 sql 即可。SQLCasesLoader 会读取，反序列化，加载这些信息到一个 map 中（Map\<String, SQLCase> sqlCases）

### 断言
前面我们已经以 xml 的格式填写了我们期待进行解析的 SQL，SQLCasesLoader 也会将其加载至内存中，这时候我们来编写需要断言的相关数据，我们可以来到sharding-core-parse模块下的sharding-core-parse-test模块看一看。

我们节选一段 resources 下的 xml 文件内容如下 : 

```xml
<parser-result-sets>
    <parser-result sql-case-id="delete_with_sharding_value" parameters="1000, 1001, 'init'">
        <tables>
            <table name="t_order"/>
        </tables>
        <tokens>
            <table-token start-index="12" table-name="t_order" length="7" />
        </tokens>
        <sharding-conditions>
            <and-condition>
                <condition column-name="order_id" table-name="t_order" operator="EQUAL">
                    <value index="0" literal="1000" type="int" />
                </condition>
                <condition column-name="user_id" table-name="t_order" operator="EQUAL">
                    <value index="1" literal="1001" type="int" />
                </condition>
            </and-condition>
        </sharding-conditions>
        <encrypt-conditions>
            <condition column-name="status" table-name="t_order" operator="EQUAL">
                <value index="2" literal="init" type="varchar" />
            </condition>
        </encrypt-conditions>
    </parser-result>
</parser-result-sets>
```
这里 parser-result 有一个跟 sql-case 中相同的 id，下面不同的模型，即为针对 SQL 解析后的不同断言，例如针对 table ，table token，condition 的断言等等。xml 中的这些模型，对应的 java entity 就是 integrate 下面的 jaxb 目录下的相应文件。

integrate 下的 engine 目录下包含了测试代码的入口，以 sharding 为例，ShardingParameterizedParsingTest 会引用 SQLCasesLoader 加载所有的 SQL，然后引用 ParserResultSetRegistry 加载所有 xml 中的断言内容，然后通过 junit 的 [Parameterized]()