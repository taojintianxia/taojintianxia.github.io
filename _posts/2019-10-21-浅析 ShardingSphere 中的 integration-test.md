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

### 数据准备
集成测试相对于 sql 断言的测试，要更复杂一些，这里我们需要先准备以下几类数据，用于接下来的测试 :  
  
  - 1. SQL Case : 之前提及过的，在sharding-sql-test模块下中，resources/sql 目录下的相应 xml 文件。这些 xml 文件中包含的都是 SQL 语句，可以用于很多类型的测试，这也是为什么将这个模块单独提炼出来的原因。
  - 2. Integration Test Case : 集成测试用到的数据整合文件。具体在项目中的位置就是 integration-test模块下, resources/intgrate/cases 中以 "-integrate-test-cases.xml" 结尾的文件。
  - 3. Expected Data File : 针对不同测试用例的期待数据是不同的，我们在前两个文件中定义了 SQL 语句，参数，但是我们需要针对具体的 sharding 算法进行相应的数据验证，master-slave rule 下的 sharding 数据肯定跟 tbl sharding 的断言数据不一样。具体在 ss 中对应的位置为 resources/intgrate/cases 下对应的sharding rule 目录下的 xml 文件，该文件的名字要跟 integrate-test-cases.xml 中描述的 expected-data-file 文件名相同。

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

加载了这些数据后，执行断言的具体测试类，会一层一层的调用自己父类的构造函数，直到调用所有集成测试类的父类 BaseIT 。 BaseIT 的构造函数会根据 env.properties 中的环境变量，创建相应的 DataSourceMap，接下来，系统会在 resources/integrate/env/sharding-type/sharding-rule.yaml 中获取所需的 sharding 规则，并以此创建 DataSource。


