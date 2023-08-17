---
layout: post  
title: Correct the classpath of your application so that it contains a single, compatible version of XXX  
categories: [springboot, guava]  
description: Correct the classpath of your application so that it contains a single, compatible version of XXX的解决方案    
keywords: springboot, guava  
---

#### 今天在使用swagger的时候遇到如下的错误 :
```
***************************
APPLICATION FAILED TO START
***************************

Description:

An attempt was made to call the method com.google.common.collect.FluentIterable.concat(Ljava/lang/Iterable;Ljava/lang/Iterable;)Lcom/google/common/collect/FluentIterable; but it does not exist. Its class, com.google.common.collect.FluentIterable, is available from the following locations:

    jar:file:/Users/user/Work/Repositories/maven_repositories/com/google/guava/guava/18.0/guava-18.0.jar!/com/google/common/collect/FluentIterable.class

It was loaded from the following location:

    file:/Users/user/Work/Repositories/maven_repositories/com/google/guava/guava/18.0/guava-18.0.jar


Action:

Correct the classpath of your application so that it contains a single, compatible version of com.google.common.collect.FluentIterable


Process finished with exit code 1
```  

#### 发生的原因是因为有几个框架都依赖了不同版本的guava, 我在不同的框架中引入exclusion, 去掉了相关框架对guava的依赖, 并统一使用guava 18.0 :
```
<dependency>
  <groupId>com.google.guava</groupId>
  <artifactId>guava</artifactId>
  <version>18.0</version>
</dependency>
```
#### 但是经查验, FluentIterable下的concat方法是guava 20.0才加上的方法, 18.0中当然不包含该方法.
#### 于是将guava升级为20.0, 问题解决
