---
layout: post  
title: 浅析 ShardingSphere 中的 integration-test.  
categories: [shardingsphere, test]  
description: 浅析 ShardingSphere 中的 integration-test.  
keywords: shardingsphere, test    
---

### 简介
前文提到过，ShardingSphere 为了针对不同数据库，不同的 sharding 类型的测试，开发了一套测试引擎。今天我们就来聊一聊其中的集成测试引擎。

该引擎位于 sharding-integration-test 模块。integration-test 知名见意，就是集成测试引擎，在这个引擎中，主要分为两个维度的测试 : 

  - sharding 策略的测试 : 分库分表、仅分表、仅分库、读写分离等策略
  - JDBC 维度的测试 : 针对 Statement、PreparedStatement 的测试

integrate-test 代码量明显比前面提到的 sql-test 多很多，结构也相对复杂一些，阅读起来需要有耐心。

### 配置文件

不同于 sql 解析引擎的测试，集成测试需要一个真实的测试环境(我们可以安装相应的数据库，不过更推荐 docker 的方式)，我们需要在 resource/integrate 下的env.properties 中进行配置 :

```
# 测试主键，并发，column index 等的开关
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

通过以上的配置，我们可以看到，要测试的数据库类型为 MySQL，相关的用户名密码以及host 都已经配置好，要测试的 sharding 类型为分库，分表，分库分表主从，主从

### 数据准备
集成测试相对于 sql 断言的测试，要更复杂一些，这里我们需要先准备以下几类数据，用于接下来的测试 :  
  
  - 1.SQL Case : 之前提及过的，在sharding-sql-test模块下中，resources/sql 目录下的相应 xml 文件。这些 xml 文件中包含的都是 SQL 语句，可以用于很多类型的测试，这也是为什么将这个模块单独提炼出来的原因。
  - 2.Integration Test Case : 集成测试用到的数据整合文件。具体在项目中的位置就是 integration-test模块下, resources/intgrate/cases 中以 "-integrate-test-cases.xml" 结尾的文件。
  - 3.Expected Data File : 针对不同测试用例的期待数据是不同的，我们在前两个文件中定义了 SQL 语句，参数，但是我们需要针对具体的 sharding 算法进行相应的数据验证，master-slave rule 下的 sharding 数据肯定跟 tbl sharding 的断言数据不一样。具体在 shardingsphere 中对应的位置为 resources/intgrate/cases 下对应的sharding rule 目录下的 xml 文件，该文件的名字要跟 integrate-test-cases.xml 中描述的 expected-data-file 文件名相同。

### 数据加载
integration-test 中还是用到了 junit 的 Parameterized，所以根据 @Parameters 进行查找，就可以找到测试相应的启动参数了。我们可以看到，Parameterized 启动的时候会加载 SQL CASE 信息以及 Integrate test 的信息。并将这两部分信息进行组合，包装为一个 Collection<Object[]> 数据，我们可以看一看这个 object 都包含了哪些数据 : 

```
# sql case 的 id，寻找定义 sql 的途径
object[0] = "select_alias_as_keyword"

# 与 sql 操作类型对应的需要校验数据的“索引”文件的地址
object[1] = "/xxxx/Github/incubator-shardingsphere/sharding-integration-test/sharding-jdbc-test/target/test-classes/integrate/cases/dql/dql-integrate-test-cases.xml"

# 根据 sql id 定位的断言数据的信息，包含要断言数据的文件地址，以及参数
object[2] = {DQLIntegrateTestCaseAssertion} 

# sharding 的类型
object[3] = "masterslave"

# 要断言的数据库类型以及是否支持该数据库（例如 "H2:Disabled"）
object[4] = {DatabaseTypeEnvironment} 

# sql 的类型，对应普通 sql 以及 prepared 的 sql
object[5] = {SQLCaseType@2831} "Literal"
```

加载了这些数据后，执行断言的具体测试类，会一层一层的调用自己父类的构造函数，直到调用所有集成测试类的父类 BaseIT 的构造函数。

然而，在这个调用的过程中，dcl，dml，dql 的 @BeforeClass 进行了建库建表的行为，ddl 仅仅是创建了数据库。建库表的原信息来自于 resources/integrate/env/相应的SQL类型/schema.xml，schema.xml 中定义了测试需要创建的库表信息。

在此之后，系统调用了 BaseIT的构造函数。BaseIT 的构造函数会根据 env.properties 中的环境变量，创建相应的 DataSourceMap，接下来，系统会在 resources/integrate/env/sharding-type/sharding-rule.yaml 中获取所需的 sharding 规则，并以此创建 DataSource。

到目前为止，一切数据都已经准备好了，只等待着断言方法的执行。

![](https://taojintianxia.github.io/images/posts/shardingsphere/test/integration-test/integration-test.jpg)

让我们再回到之前那个集成测试类 GeneralDMLIT， 我们看看这个类的断言接下来会做什么。鉴于前面准备了好多好多的各种数据，我们目前一定会获取到如下数据 : 

  - Sql case id
  - 我们要执行的 SQL
  - 如果有需要，我们也会获取到 SQL 对应的参数，以及参数的类型
  - 实现写好的该 sql 对应的断言数据

我们只需要组装出一条可执行的 SQL，执行后对结果做如下断言，就可以判断 SQL 的执行结果是否符合预期了 :
 
  - 1.将 sql 执行后返回的数据 count 跟 预期的 sql count 进行对比
  - 2.将 sql 执行后返回的所有列名，按顺序，名称，数量一一与期待值进行对比
  - 3.将每一行的数据，按出现的位置，值，进行一一对比，确定与期待的数据是一致的。

至此，所有的测试数据，都会一条条的按顺序进行断言，直至结束。



