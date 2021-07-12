---
layout: post  
title: 为 Grafana 添加 JVM 的数据  
categories: [grafana, prometheus, jvm]  
description: 为 Grafana 添加 JVM 的数据  
keywords: grafana, prometheus, jvm  
---

## 为 Grafana 添加 JVM 的数据

### 简介

JVM 的相关数据是观察、衡量甚至排查一个 Java 应用的比较重要的指标。主要观察点在于 JVM 的内存使用、分配、不同垃圾回收器的触发，以及垃圾回收后 JVM 的状态等。

同其他常见的 metric 一样，prometheus 也提供了 JVM 的 exporter：JMX Exporter

### JMX Exporter 的安装跟配置

