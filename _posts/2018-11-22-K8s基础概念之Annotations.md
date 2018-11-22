---
layout: post  
title: K8s基础概念之Annotations  
categories: [kubernetes, Annotations]  
description: K8s基础概念之Annotations  
keywords: kubernetes, Annotations  
---

## Annotations

#### Annotations 是 key/value 形式附加于对象的注解。不同于 Labels 用于标志和选择对象，Annotations 则是用来记录一些附加信息，用来辅助应用部署、安全策略以及调度策略等。比如 deployment 使用 annotations 来记录 rolling update 的状态。