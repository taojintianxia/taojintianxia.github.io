---
layout: post  
title: OpenGauss 的安装  
categories: [opengauss, 安装]  
description: OpenGauss 的安装  
keywords: opengauss, 安装  
---

# OpenGauss 的安装

OpenGauss 是华为基于 PGSQL 打造的开源数据库，本文将介绍如何详细安装 OpenGauss

## 准备 OpenGauss 的安装文件
访问 OpenGauss 的 [下载页面](https://opengauss.org/zh/download.html)，复制企业版下载的链接，然后在我们要安装的机器上通过命令行进行下载并解压，例：

```
mkdir -p /var/tmp/opengauss
cd /var/tmp/opengauss
wget https://opengauss.obs.cn-south-1.myhuaweicloud.com/2.0.1/x86/openGauss-2.0.1-CentOS-64bit-all.tar.gz
tar -zxvf openGauss-2.0.1-CentOS-64bit-all.tar.gz
chmod 755 var/tmp/opengauss
```

来到 OpenGauss 的 [配置文件模板页面](https://opengauss.org/zh/docs/2.0.1/docs/installation/创建XML配置文件.html)，这里我们按需复制 `单节点配置文件` 并修改为如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<ROOT>
    <!-- openGauss整体信息 -->
    <CLUSTER>
        <!-- 数据库名称 -->
        <PARAM name="clusterName" value="dbCluster" />
        <!-- 数据库节点名称(hostname) -->
        <PARAM name="nodeNames" value="centos71" />
        <!-- 数据库安装目录-->
        <PARAM name="gaussdbAppPath" value="/opt/opengauss/install/app" />
        <!-- 日志目录-->
        <PARAM name="gaussdbLogPath" value="/var/log/omm" />
        <!-- 临时文件目录-->
        <PARAM name="tmpMppdbPath" value="/opt/opengauss/tmp" />
        <!-- 数据库工具目录-->
        <PARAM name="gaussdbToolPath" value="/opt/opengauss/install/om" />
        <!-- 数据库core文件目录-->
        <PARAM name="corePath" value="/opt/opengauss/corefile" />
        <!-- 节点IP，与数据库节点名称列表一一对应 -->
        <PARAM name="backIp1s" value="10.12.3.28"/> 
    </CLUSTER>
    <!-- 每台服务器上的节点部署信息 -->
    <DEVICELIST>
        <!-- 节点1上的部署信息 -->
        <DEVICE sn="centos71">
            <!-- 节点1的主机名称 -->
            <PARAM name="name" value="centos71"/>
            <!-- 节点1所在的AZ及AZ优先级 -->
            <PARAM name="azName" value="AZ1"/>
            <PARAM name="azPriority" value="1"/>
            <!-- 节点1的IP，如果服务器只有一个网卡可用，将backIP1和sshIP1配置成同一个IP -->
            <PARAM name="backIp1" value="10.12.3.28"/>
            <PARAM name="sshIp1" value="10.12.3.28"/>
               
	    <!--dbnode-->
	    <PARAM name="dataNum" value="1"/>
	    <PARAM name="dataPortBase" value="15400"/>
	    <PARAM name="dataNode1" value="/opt/opengauss/install/data/dn"/>
            <PARAM name="dataNode1_syncNum" value="0"/>
        </DEVICE>
    </DEVICELIST>
</ROOT>
```

该文件修改主要注意以下几点：

  - 替换 hostname
    - 在机器上执行命令 `cat /etc/hostname`，获取机器的 hostname，这里假设为 `centos71 `
    - 用 `centos71 ` 替换所有的 `node1_hostname `
  - 设置 ip
    - 在机器上执行 ifconfig 并记录主机的 ip 地址
    - 将模板中所有的 `192.168.0.1` 都替换为主机 ip
  - 设置 datanode 安装路径
    - 将模板中的 `/opt/huawei/install/data/dn` 替换为我们自定义的安装地址，例如 `/opt/opengauss/install/data/dn"/`

将该 xml 模板内容保存到一个文件中，例如 /opt/opengauss/install_config.xml

## 安装 OpenGauss

