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

首先下载 JMX Exporter：

```
wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.16.0/jmx_prometheus_javaagent-0.16.0.jar
```

创建其使用的 config.yaml

```
---
startDelaySeconds: 0
ssl: false
lowercaseOutputName: true
lowercaseOutputLabelNames: true
```

在要启动的 jar 上配置 javaagent，例如 ss proxy 中，在 start.sh 中添加：

```
# /opt/sphere-ex/prometheus_exporter/ 为存放 agent 的路径
java -javaagent:/opt/sphere-ex/prometheus_exporter/jmx_prometheus_javaagent-0.16.0.jar=127.0.0.1:8080:/opt/sphere-ex/prometheus_exporter/jmx_prometheus_javaagent_config/config.yaml
```

通过 `sh start.sh` 启动 proxy，这时候我们可以看到对应端口已经提供了 metric 服务

```
curl 127.0.0.1:8080/metric
```

接下来配置一下 Prometheus。

在 Prometheus 的配置文件中，添加如下配置：

```
scrape_configs:
  - job_name: 'java'
    static_configs:
    - targets: ['YOUR_IP:YOUR_PORT']
```

重新 Prometheus 后即可生效。

在 Grafana 的 [dashboard](https://grafana.com/grafana/dashboards) 上搜索相关模板，安装后即可显示

