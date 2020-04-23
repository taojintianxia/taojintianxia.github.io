---
layout: post  
title: Sharding-Scaling 系列之一 介绍  
categories: [shardingsphere, sharding-scaling, 介绍]  
description: Sharding-Scaling 系列之一 介绍  
keywords: shardingsphere, sharding-scaling, 介绍
---

# Sharding Scaling 之 介绍

本系列是一组记录自己学习 `sharding scaling` 的文章。通过源码阅读的方式，尝试深入了解 `sharding scaling`。不会仅仅简单的摘抄官方文档，力求在学习的过程中得出属于自的理解。

## 背景

谈及 sharding scaling，应该先了解一下 [shardingsphere](https://github.com/apache/shardingsphere) 这个项目。这是一套非常强大的数据分片（也就是分库分表）的生态环境，`sharding scaling` 就是其中的一个模块。

sharding scaling 主要是解决两个问题的 : 

  - 旧库迁移：有些单表过亿，或者数据大小超过 1 Tb 的数据库，急需迁移并使用 shardingsphere。sharding scaling 可以将旧库迁移到分片后的多个新数据库中
  - 重新分片：由于业务升级很快超出负荷或者某些原因，原有的分片需要进行重新分片，这也是 sharding scaling 支持的范围

## 架构
 
 如下为官方提供的 4.0.1 版本的架构图 ：
 
 ![](https://taojintianxia.github.io/images/posts/shardingsphere/scaling/scaling-overview.cn.png) 
 
通过这份架构图，我们可以看出 scaling 的几个步骤 ：
 
 1. 用户在 'UI' 里修改 shardingsphere 的配置
 2. 修改配置的请求发送到 sharding scaling，scaling 创建两个 job
   - 存量数据同步
   - 增量数据同步
 3. 增量数据同步到一定程度，达到数据校验后的阈值，会向 zk 发送持久化的消息。
 4. 用户从某个地方看到 zk 上的消息，在 UI 界面上手动暂停正在同步数据的数据库。
 5. 增量任务将后续新进的数据分片掉后，修改 zk 上保存的用户配置，以达到切换 shardingsphere 配置的目的。

然而通过这份图，我对 sharding scaling 有以下几点的顾虑：
 
 1. 数据迁移并没有依赖外部的作业中间件，自己开发作业任务其实是发明轮子了。
 2. 不是分布式的，这样可能会造成单点问题以及吞吐量不足。
 3. 校验增量同步数据并没有提及触发了阈值，持久化后如何通过 zk 通知用户。
 4. 目前停止原始库是通过手动的方式，不科学。
 5. 设置只读后的请求，是抛弃了还是堆积，如果抛弃了，怕是用户体验不会太好。

这里在原图的基础之上，按照自己的假想，画了一张我期待的 scaling 的结构图

 ![](https://taojintianxia.github.io/images/posts/shardingsphere/scaling/new-architecture.png) 
 
 画了这张图后，我还是没想好数据库  `只读`  后向数据库发送的请求应该怎么处理，也想过如果有可能，提供 Hikari 的一个扩展，把只读后的数据库请求缓存起来，等切换了数据源后，再发送这些请求。但是这样做收效很低，反而导致风险很大。相信 sharding scaling 后续还会再这点上有考虑吧，毕竟切成只读后的几秒到几十秒会导致用户体验变差。
 
## 问题

一个就是 `断点续传`。在迁移数据的过程中，不可能永远顺顺利利，中途出现问题是必须要考虑的。出了问题后，要在恢复的过程中续传后续数据，否则就需要清空新分片中的所有数据重新传送，这也是非常糟糕的。

还有就是目前迁移时候的语句，是一条数据一条 `INSERT` 的 SQL，这样可能会导致跟 proxy 的通讯增加。是否可以积累一定数据后统一发给 proxy，以 INSERT Batch 的形式插入数据也是个值得探究的点。

再就是针对 `DDL` 应该做一些测试，仅仅是扫描当前旧库的表结构就在分片上创建对应的表，遇到 DDL 会不会报错？(例如当前旧库的表叫 current_table，在分片上创建好相同的表结构后，读取bin log 的同时，遇到了 `alter table  origen_table rename to current_table ` 是会报错的 )

## 现状

目前的 sharding scaling 只是个 Alpha 版本，提供了基础的使用方法，后续需要增强的地方还有很多。

如下图就是目前官网提供的 RoadMap :

 ![](https://taojintianxia.github.io/images/posts/shardingsphere/scaling/original-roadmap.cn.png) 

通过这张图，我们可以看到 sharding scaling 后续的里程碑是要支持断点续传甚至自动扩缩容的。这就非常让人期待了。

## 总结

sharding scaling 作为 shardingsphere 生态网络中的新的一环，可以将单库进行分片，对需要重新分片的数据库集群进行扩缩容，是数据迁移过程中非常重要的一部分。

而且目前 shardingsphere 的其他模块已经相对成熟，想要加入社区为社区贡献力量，不妨先从 sharding scaling 上手，可参与的程度会更高。

相信在一定程度的打磨后，sharding scaling 甚至可以独立成一个单独的项目，提供给其他开源的分库分表项目使用。就像我相信将来 SQL 解析这块也会提炼成独立的引擎。