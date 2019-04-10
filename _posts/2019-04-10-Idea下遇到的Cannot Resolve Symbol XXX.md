---
layout: Idea下遇到的Cannot Resolve Symbol XXX  
title: Idea下遇到的Cannot Resolve Symbol XXX    
categories: [idea]  
description: Idea下遇到的Cannot Resolve Symbol XXX  
keywords: idea    
---

### Cannot Resolve Symbol XXX

#### 一般来说, idea下遇到这类问题, 多半是缓存的问题, 毕竟工作几年的人, 不可能再因为忘记引入xxx依赖导致这种问题.这里就记录下常见的解决办法吧

#### 1. 是不是忘记引入依赖了, 在根目录下mvn clean compile下, 如果mvn都执行失败, 那就先把依赖问题解决下.

#### 2. 看看repository下是否有这个包, 一般在.m下, 没有就去项目里mvn clean install

#### 3. 编译器更换了, 例如从JDK1.8-u181升级到JDK1.8u-201, 原来的181删掉了, 不过idea一般是会有提示的.更新JDK : File - Project Structure - Project SDK, 看看目前的JDK是不是那个失效的

#### 4. 清除缓存, File - Invalidate Caches / Restart, 然后点击Invalidate and Restart, 一般这么做就没什么问题了.
