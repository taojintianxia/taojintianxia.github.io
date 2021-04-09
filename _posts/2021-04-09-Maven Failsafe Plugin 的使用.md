---
layout: post  
title: Maven Failsafe Plugin 的使用  
categories: [maven, failsafe]  
description: Maven Failsafe Plugin 的使用  
keywords: maven, failsafe  
---

## Maven 的 Failsafe 插件（草稿）

Failsafe 是一个用于运行集成测试的 maven 插件，起这么个名字是因为 failsafe 是 surefire 的同义词，同时也意味着当执行失败的时候，会以一种安全的方式退出

该插件主要有 4 个阶段用来执行集成测试：

  - pre-integration-test：设置集成测试的环境
  - integration-test：运行集成测试
  - post-integration-test：清理集成测试环境
  - verify：检查集成测试的结果

如果你用 Surefire 插件运行测试，一旦有用例运行失败，在 `integration-test ` 阶段构建就会立刻失败，进而导致无法进行测试环境的清理