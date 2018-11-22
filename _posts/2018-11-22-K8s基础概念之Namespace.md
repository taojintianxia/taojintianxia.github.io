---
layout: post  
title: K8s基础概念之Namespace  
categories: [kubernetes, Namespace]  
description: K8s基础概念之Namespace  
keywords: kubernetes, Namespace  
---

#### Namespace 是对一组资源和对象的抽象集合，比如可以用来将系统内部的对象划分为不同的项目组或用户组。常见的 pods, services, replication controllers 和 deployments等都是属于某一个 namespace 的（默认是 default），而 node, persistentVolumes 等则不属于任何 namespace。
