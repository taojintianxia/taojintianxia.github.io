---
layout: post  
title: ss-agent 配合 Prometheus 的使用  
categories: [agent, prometheus, grafana]  
description: ss-agent 配合 Prometheus 的使用  
keywords: agent, prometheus, grafana  
---

## ShardingSphere 中 agent 的使用

agent 模块是 shardingsphere 中用于提供 metrics 数据的，原理跟 APM 的探针一样，都是通过 agent 将埋点处的相关数据暴露出去，提供对 shardingsphere 相关数据的监控。

也就是说 ss-agent 就是一个 ss 内部提供的，一个按照不同规范使用的 APM 探针。