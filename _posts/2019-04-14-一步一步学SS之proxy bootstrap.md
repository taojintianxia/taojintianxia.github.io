---
layout: post  
title: 一步一步学ss之proxy bootstrap  
categories: [proxy, bootstrap, shardingsphere, ss]  
description: 一步一步学ss之proxy bootstrap  
keywords: proxy, bootstrap, shardingsphere, ss  
---

## Sharding Proxy
### 简介
sharding proxy是我开始接触的第一个模块, 在此之前, 我一直觉得sharding-jdbc就是个分库分表的小工具, 仅仅是一个对开发人员透明的优秀ORM插件罢了. 可是分表之后的数据，对使用客户端进行操作的 DBA 来说，是非常不友好的，自从sharding proxy出现后, 这类问题就得到了有效的解决，proxy明显把shardingsphere整合到了一个新的高度, 我就是从这里开始入手学习sharding sphere的

官方对sharding proxy的定义是 "它定位为透明化的数据库代理端，提供封装了数据库二进制协议的服务端版本，用于完成对异构语言的支持。 目前先提供MySQL版本，它可以使用任何兼容MySQL协议的访问客户端(如：MySQL Command Client, MySQL Workbench, Navicat等)操作数据，对DBA更加友好。"
 - 向应用程序完全透明，可直接当做MySQL/PostgreSQL使用。
 - 适用于任何兼容MySQL/PostgreSQL协议的的客户端。

 Sharding-Proxy的优势在于对异构语言的支持，以及为DBA提供可操作入口。

### 架构图

如图为sharding proxy的架构图：
![](https://taojintianxia.github.io/images/posts/shardingsphere/proxy/sharding-proxy-brief_v2.png)  

### 代码
bootstrap 是 proxy 的启动入口，先从这里的代码读起

### Bootstrap
bootstrap 是 sharingsphere 的启动点，这个类里主要做了几件事：  
1. 加载配置  
2. 启动 register center(本地或远程)  
3. 启动 netty

#### 1. 加载配置
 - 1.1 加载server配置  

```
## 这里我们看一下ShardingConfigurationLoader的 load() 方法
## 第一步加载 server 的相关信息（server.yaml）
YamlProxyServerConfiguration serverConfig = loadServerConfiguration(new File(ShardingConfigurationLoader.class.getResource(CONFIG_PATH + SERVER_CONFIG_FILE).getFile()));

## 加载 server.yaml 后，会通过YamlEngine反序列化yaml文件中的配置到YamlProxyServerConfiguration

```

 - 1.2 加载proxy rule 配置  

```
## 加载 rule 的配置，默认在项目的resources/conf目录里
File configPath = new File(ShardingConfigurationLoader.class.getResource(CONFIG_PATH).getFile());

## 这里我们可以看到，代码会通过这个正则，扫描配置目录里所有以config-开头的yaml文件
Pattern RULE_CONFIG_FILE_PATTERN = Pattern.compile("config-.+\\.yaml");
```

#### 2. 
