---
layout: post  
title: Sharding-Scaling 系列之二 BootStrap  
categories: [shardingsphere, sharding-scaling, bootstrap]  
description: Sharding-Scaling 系列之二 BootStra  
keywords: shardingsphere, sharding-scaling, bootstrap
---

# Sharding Scaling 之 BootStrap

目前的文章是按照当前源码的包结构去一个一个的看的，所以最开始读源码，就是 `bootstrap` 。按当前的代码结构，后面还会有 `core`，`mysql`  以及 `postgresql` 的理解。


## 概览

bootstrap 是一个 netty 编写的基础服务，作为 sharding scaling 的入口，bootstrap 会在读取 /conf/ 下的 `server.yaml` 之后，启动一个 Netty server。进而等待客户端发送的请求并加以处理。

我也想过，其实这里用 Springboot MVC 是比较方便的，但是 shardingsphere 除了扩展，并没有引入 Spring 管理各种类，一直都是 SPI 跟 Factory。相信 netty 会更加高效，而且也不会引入太多额外的包吧，至少启动速度比 springboot mvc 快很多。

## 流程

Bootstrap 的启动类在 `Bootstrap.java` 中。main 方法会启动一个 netty server。在启动 server 之前，bootstrap 会读取 /conf/ 下的 `server.yaml`

```java
private static void initServerConfig() throws IOException {
	  // 根据路径获取 yaml 文件
        File yamlFile = new File(RuntimeUtil.getResourcePath(DEFAULT_CONFIG_PATH + DEFAULT_CONFIG_FILE_NAME));
        // 将 yaml 文件 unmarshal 成一个 ServerConfiguration 类
        ServerConfiguration serverConfiguration = YamlEngine.unmarshal(yamlFile, ServerConfiguration.class);
        // guava 中的检查方法，如果 serverConfiguration 是 null，则抛出 NullPointerException
        Preconditions.checkNotNull(serverConfiguration, "Server configuration file `%s` is invalid.", yamlFile.getName());
        // 初始化一个 DefaultSyncTaskExecuteEngine，后面会有说明。
        ScalingContext.getInstance().init(serverConfiguration);
    }
```

读取到配置文件后，netty 会根据配置文件中的端口启动 server : 

```java
EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap bootstrap = new ServerBootstrap();
            bootstrap.option(ChannelOption.SO_BACKLOG, 1024);
            bootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .handler(new LoggingHandler(LogLevel.INFO))
                    .childHandler(new HttpServerInitializer());
            int port = ScalingContext.getInstance().getServerConfiguration().getPort();
            Channel channel = bootstrap.bind(port).sync().channel();
            log.info("ShardingScaling is server on http://127.0.0.1:" + port + '/');
            channel.closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
```
这里需要注意的是，如果想本地调试 sharding scaling，需要添加对应的数据库 driver，以 MySQL 为例，在 bootstrap 的 pom.xml 中添加驱动如下：

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
    <scope>runtime</scope>
</dependency>
```
这里记得要将 scope 设置为 runtime 才可以。
