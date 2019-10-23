---
layout: post  
title: 浅析 ShardingSphere中的 sql-test  
categories: [ss, test]  
description: 浅析 ShardingSphere中的 sql-test  
keywords: ss, test  
---

### 简介
sharding sphere 为了保证代码质量，提供了完善的测试引擎。引擎使用 xml 数据驱动，可以针对 H2，MySQL，Oracle，PostgreSQL 进行相应的测试。

sharding-sql-test 是一个新抽取出的模块，主要用于 SQL 解析后的断言验证。在 sharding sphere 中，sql 解析是个比较复杂的部分，在非常谨慎的情况下做的任何修改，也无法保证解析后的正确性，针对解析的断言，是质量保证的门槛。这里我们简单介绍下针对 SQL 解析的断言的处理方式。

### 加载
sql-test 模块下，有一个 SQLCasesLoader 类，该类会按照根据指定的参数，递归加载 resources 下对应目录中的 xml 文件。这个类是 sql-test 模块的核心功能，所有的数据加载，都是这个类完成的。

我们举个例子，需要编写的 xml 文件的格式如下 : 

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

这时，所有我们想要进行解析的 SQL 就在这里填写完毕了。

### 断言
前面我们已经以 xml 的格式填写了我们期待进行解析的 SQL，SQLCasesLoader 也会将其加载至内存中，这时候我们来编写需要断言的相关数据。我们可以到sharding-core-parse模块下的sharding-core-parse-test模块看一看。

节选一段 resources 下的 xml 文件内容如下 : 

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
这里可以看到， parser-result 有一个跟 sql-case 中相同的 id，下面不同的模型，即为针对 SQL 解析后的不同断言，例如针对 table ，table token，condition 的断言等等。xml 中的这些模型，对应的 java entity 就是 integrate 下面的 jaxb 目录下的相应文件。

integrate 下的 engine 目录下包含了测试代码的入口，以 sharding 为例，ShardingParameterizedParsingTest 会引用 SQLCasesLoader 加载所有的 SQL，然后引用 ParserResultSetRegistry 加载所有 xml 中的断言内容，然后通过 junit 的 [Parameterized](https://github.com/junit-team/junit4/wiki/parameterized-tests)方式，将所有的参数，批量执行，最大化的减少因测试数据不同的重复用例。

断言是根据 SQL 的类型进行分配的。每次Junit 的@Parameters 将sqlCaseId，databaseType，sqlCaseType组成一组数据，调用 assertSupportedSQL 方法。该方法首先会根据参数获取到sql case 中我们事先定义好要去解析的 SQL 语句，然后将该语句交给 SQL 解析引擎（SQLParseEngine）去处理，并返回 SQLStatement 。接下来，断言引擎（ShardingSQLStatementAssert）会根据分析返回的SQLStatement

有了这个断言引擎，我们无需编写任何 Java 代码，只需要在两个地方定义好相关的数据 :

  - 1. 在 sql-case 中填写 一个id，以及我们要断言的 SQL 语句
  - 2. 在 parser-result 中填写期待的解析数据

不过目前来说，断言引擎中的 if 太多，接下来我会看看有限状态机的效率，不排除会在不影响效率的情况下，提交有限状态机的重构 PR。

ss 中的 SQL 解析本质上的稍微复杂的，对初学者并不太友好，但是有了断言引擎，我们可以根据解析后的 statement 中的数据进行合理断言，排除掉解析的复杂性，以至于初学用户也完全可以在不了解解析引擎的情况下编写断言数据。
