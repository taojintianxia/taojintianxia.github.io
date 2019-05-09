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

## 每一个 rule 都会被反序列化为YamlProxyRuleConfiguration，可以是 master-slave 的配置，也可以是 sharding 的配置。
 File configPath = new File(ShardingConfigurationLoader.class.getResource(CONFIG_PATH).getFile());
        Collection<YamlProxyRuleConfiguration> ruleConfigurations = new LinkedList<>();
        for (File each : findRuleConfigurationFiles(configPath)) {....}
        
## 不过这里我有些诟病的是，loadRuleConfiguration(final File yamlFile, final YamlProxyServerConfiguration serverConfiguration)这个方法
## 传入的serverConfiguration参数，仅仅是为了校验，显得不够优雅

## 每读取一个配置文件，都会进行 checkstate 的检查，有问题会立刻报错
```

#### 2. 接下来会根据读取到的配置，分成两种启动方式 ：  
 - 2.1 没有 注册中心 的启动方式
 - 2.2 通过 zk 等注册中心读取相关配置的启动方式

没有注册中心的启动方式 :  

```
## 1. 初始化ShardingProxyContext
ShardingProxyContext.getInstance().init(authentication, prop);

## 这里对ShardingProxyContext.getInstance的初始化包括读取数据库的用户名/密码，相关 properties(主要是数据库的一些配置).
## 同样，读取配置后，还是会进行一系列的校验

## 2. 初始化 logic schema
## 首先会根据数据库连接获取数据库类型，但是这里需要小吐槽下，这段代码可读性也不太好
schemaDataSources.values().iterator().next().values().iterator().next().getUrl()
## 还有一点，就是final boolean isUsingRegistry这样的变量，其实不需要 is 的前缀的

## 3. 初始化 opentracing
## 具体代码没有细跟，只是看到了LOGGER.log(Level.FINE, "Attempted to register the GlobalTracer as delegate of itself.");
## 而且 apm 类的东西也不是 ss 的重点，有时间了解下即可

## 4. 启动 ShardingProxy
这里根据配置文件中定义好的端口启动了一个 netty server

```

有注册中心的启动方式：  

```
## 1. 有注册中心的代码就明显不一样了，看起来骚气很多啊（这里我甚至怀疑，有注册中心跟没注册中心的代码，不是出自一个人）
## 代码一开始，就用了 try with resources, Facade patten.
try (ShardingOrchestrationFacade shardingOrchestrationFacade = new ShardingOrchestrationFacade(
                new OrchestrationConfigurationYamlSwapper().swap(serverConfig.getOrchestration()), shardingSchemaNames))
                
## 但是说实话，这里的这个 Facade，看起来并不是一个 facade 模式，swap 用的也稍显牵强。这的代码虽然风格华美一些，但是并没有前面代码的那种朴实实用感。此处怀疑作者是不是第二人格发作了, 笑              

## 值得一提的是，RegistryCenter的 load 方法，还是有些水平的。
## 我本以为配置文件中定义好针对不同的注册中心的配置，然后启动的时候加载即可。没想到，这里是通过 SPI 的方式去处理注册中心的
## 配置文件中，只保存serverLists: localhost:2181，namespace: orchestration。这类抽象的配置，注册中心是个抽象的接口，使用 SPI 去加载，加载哪种注册中心就启动哪种注册中心。
## 如果加载的过程中发现有多个 SPI 的实现，那就默认取最后一个找到的 SPI 实现
             
## 剩下的代码就跟没有注册中心一样了
## 2. 同样初始化 ShardingProxyContext
## 3. 同样初始化LogicSchemas
## 4. 同样初始化OpenTracing
## 5. ShardingProxy启动 netty server 
```
#### 至此 bootstrap 的代码就结束了，我们可以看到，bootstrap 的代码主要是加载、检查配置并启动 proxy。