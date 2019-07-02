---
layout: post  
title: Apache Calcite 简介（一）  
categories: [calcite, 简介]  
description: Apache Calcite 简介（一）  
keywords: calcite, 简介  
---

## 挥动 SQL 的舞者 -- Calcite

第一次听到 calcite , 还是一个朋友跟我说起 CBO 的事，然后花了一点时间，尝试着学习一下 Calcite 到底是何方神圣。

谈起 Calcite 之前，可能很多 team 中都会有到底公司内用 golang 的多，还是用 Java 的多，PHP 是不是最好的语言，前端技术栈深还是后端更难的无聊争论，但是，无论怎么争论，有一点很容易被大伙忽视的事实，那就是大部分公司内，用的最多的，会的最多的，最好上手的语言，其实是 SQL。

### Calcite 简介
SQL 算起来已经是一种比较古老的语言了，但是仍然非常活跃的出现在任何数据持久化的场景中，哪怕数据存储使用的不是关系型数据库。然而这跟 Calcite 有什么关系？我们看看 Calcite 官网的介绍：

#### The foundation for your next high-performance database.（下一代高性能数据库的基石）

这不是在 tree new bee 吧？然而看到Calcite 的使用者的时候，真是觉得，Calcite 还是 new bee 的，居然有这么多工具在用：  
![](https://taojintianxia.github.io/images/posts/calcite/powered-by.png)

汗！。。。还是回来认认真真学习一下 calcite 吧：
在 calcite 的官网上，记录了 calcite 的三个特性：  
  - Standard SQL
  	- 行业标准的SQL解析器，校验器和JDBC驱动程序
  - Query optimization
    - 通过关系代数描述查询，使用计划规则进行转换，并根据成本模型进行优化。 
  - Any data, anywhere
  	- 根据推送到数据的计算规则，连接第三方数据源，浏览元数据并进行优化

这里就描述了 Calcite 的三个主要功能，1.SQL 的解析校验，2查询优化，3 数据的获取  
不过大家有没有注意到，一个 SQL“工具”，然没有任何数据管理的功能。这其实也是 Calcite 非常优秀的设计

### Calcite 架构图
我们看一眼架构图：

![](https://taojintianxia.github.io/images/posts/calcite/architecture.jpg)

通过架构图我们可以看到，我们可以通过 JDBC 客户端连接 calcite，经过 SQL 解析校验，Query 优化后进行数据查询。然而 calcite 的数据源，都是plugable 的第三方数据源，只要实现相应的 adapter，一切符合标准的数据源，都可以通过 SQL 进行查询。

Calcite 只关注 SQL 相关，足够简单，足够专注，但是不触及具体对接的数据管理，保证了其独立性。这也是为什么大数据的工具不断的变换，但是想要套一个 SQL 的壳子，都选用 Calcite 的原因。 



