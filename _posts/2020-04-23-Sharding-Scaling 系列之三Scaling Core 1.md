---
layout: post  
title: Sharding-Scaling 系列之三 Scaling Core 1  
categories: [shardingsphere, sharding-scaling, scaling core]  
description: Sharding-Scaling 系列之三 Scaling Core 1  
keywords: shardingsphere, sharding-scaling, scaling core
---

# Sharding Scaling 之 Core 1

`core` 模块是 sharding scaling 的核心，所有关于 sharding scaling 的作业调度，存量迁移，增量迁移的相关设计，都是在 core 中。理解了 core，就理解了 sharding scaling。理解了 core，就可以按照自己的需求，添加相应 SPI 去适配对应的数据库迁移。


## 概览
Core 模块下一共是 8 个包，包含了 scaling 整个生命周期的所有结构，只用一篇文章恐怕是不能完全说完的，所以我把 core 系列分成了 4 个部分，配置，调度，存量，增量。

这一节我先读一下配置的代码

## 配置
