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

Failsafe 插件会在构建生命周期的 `integration-test` 跟 `verify `  阶段执行应用的集成测试。Failsafe 插件在 `integration-test ` 阶段不会使构建失败，进而保证 `post-integration-test` 阶段也可以得到执行。

> 可以通过下面的 maven 命令运行集成测试

```shell
mvn verify
```

如果不调用 `integration-test`，`post-integration-test` 是无法执行的

Failsafe 插件会生成两种类型的报告：txt 跟 xml

默认情况下，报告会生成到 `${basedir}/target/failsafe-reports/TEST-*.xml` 