---
layout: post  
title: Mac下更新minikube  
categories: [minikube, mac]  
description: Mac下更新minikube  
keywords: minikube, mac  
---

#### 今天用minikube的时候, 提示我已经有新版本了, 就试着Upgrade Minikube了

#### -h了一下, 发现没有相关update或者upgrade的参数, 就google了一下, 答案如下 :

```
brew update
brew cask reinstall minikube
## 这时候会提示 : Error: It seems there is already a Binary at '/usr/local/bin/minikube'; not linking.
## 再执行一遍
brew cask reinstall minikube
```

#### 搞定, 收工