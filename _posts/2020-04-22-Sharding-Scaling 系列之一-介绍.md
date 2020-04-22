---
layout: post  
title: Sharding-Scaling 系列之一 介绍  
categories: [shardingsphere, sharding-scaling]  
description: Sharding-Scaling 系列之一 介绍  
keywords: shardingsphere, sharding-scaling  
---

# Sharding Scaling 介绍

本系列文章会尝试解读 sharding scaling 项目，通过源码阅读的方式，带领大家深刻了解该项目。不会简单的摘抄官方文档，力求得出属于自的理解。

## 背景

谈及 sharding scaling，应该先了解一下 [shardingsphere](https://github.com/apache/shardingsphere) 。这是一套数据分片（也就是分库分表）的生态环境。

sharding scaling 主要是解决两个问题的 : 

  - 旧库迁移：有些单表过亿，或者数据大小超过 1Tb 的数据库，急需迁移并使用 shardingsphere。sharding scaling 可以将旧库迁移到分片后的多个新数据库中
  - 重新分片：由于业务升级很快超出负荷或者某些原因，原有的分片需要进行重新分片，这也是 sharding scaling 支持的范围

 ## 架构
 
 如下为官方提供的 4.0.1 版本的架构图 ：
 
 ![](https://taojintianxia.github.io/images/posts/shardingsphere/scaling/scaling-overview.cn.png) 
 
通过这份架构图，我们可以看出 scaling 的几个步骤 ：
 
 1. 用户修改 shardingsphere 的配置
 2. 修改配置的请求发送到 shardingscaling，scaling 创建两个 job
   - 存量数据同步
   - 增量数据同步
 3. 增量数据同步到一定程度，达到数据校验后的阈值，会向 zk 发送持久化的消息。
 4. 用户从某个地方看到 zk 上的消息，在 ui 界面上手动暂停正在同步数据的数据库。
 5. 增量任务将后续新进的数据分片掉后，修改 zk 上保存的用户配置，以达到切换 shardingsphere 配置的目的。

然而通过这份图，我们也同样能看到 sharding scaling 不成熟的几个点：
 
 1. 数据迁移并没有依赖外部的作业中间件，且不是分布式的，这样可能会造成单点问题以及吞吐量不足
 2. 校验增量同步数据并没有提及触发了阈值，持久化后如何通过 zk 通知用户。
 3. 目前停止原始库是通过手动的方式，不科学。
 4. 设置只读后的请求，是抛弃了还是堆积，如果抛弃了，怕是用户体验不会太好。

这里我按照自己的假象，画了一张我理解的 scaling 可能的结构图

 ![](https://taojintianxia.github.io/images/posts/shardingsphere/scaling/new-architecture.png) 
 

 