---
layout: post  
title: 为 github 项目的 clone 加速  
categories: [git, git pull, github, 加速]  
description: 为 github 项目的 clone 加速  
keywords: git, git pull, github, 加速  
---

平时通过 `git pull` 来克隆 github 上的项目，往往速度非常不稳定，有的时候连 10kb 都达不到，我们可以通过如下方式来为 git pull 增增速

1.替换下载项目的域名

将 `github.com` 替换为 `github.com.cnpmjs.org` (或 `hub.fastgit.org`)

```
# 例如我们通过 github 克隆 shardingsphere
# 原来的方式： git clone https://github.com/apache/shardingsphere.git
# 现在的方式
git clone https://github.com.cnpmjs.org/apache/shardingsphere.git
```

2.替换 remote 的连接

```
# 这时候我们发现远程连接不是 github.com
# git remote -v
# origin	https://github.com.cnpmjs.org/apache/shardingsphere.git (fetch)
# origin	https://github.com.cnpmjs.org/apache/shardingsphere.git (push)
# 更新远程链接
git remote set-url --push origin https://github.com/apache/shardingsphere.git
git remote set-url origin https://github.com/apache/shardingsphere.git

# 这时候的地址就对了
# git remote -v
# origin	https://github.com/apache/shardingsphere.git (fetch)
# origin	https://github.com/apache/shardingsphere.git (push)
```

这时候再通过 `git pull` 来更新下最新代码即可