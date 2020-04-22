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

谈及 sharding scaling，应该先了解一下 [shardingsphere](https://github.com/apache/shardingsphere) . 

sharding scaling 主要是解决两个问题的 : 

  - 旧库迁移：有些单表过亿，或者数据大小超过 1Tb 的数据库，急需迁移并使用 shardingsphere。sharding scaling 可以将旧库迁移到分片后的多个新数据库中
  - 重新分片：由于业务升级很快超出负荷或者某些原因，原有的分片需要进行重新分片，这也是 sharding scaling 支持的范围

 架构图
 
 如下为官方提供的 4.0.1 版本的架构图 ：
 
 ![](https://taojintianxia.github.io/images/posts/shardingsphere/scaling/scaling-overview.cn.png) 
 
 