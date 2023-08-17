---
layout: post  
title: Docker Compose概览  
categories: [docker compose]  
description: Docker Compose概览  
keywords: docker compose  
---

从今天开始学习docker compose的使用 .

Compose是一个可以定义并运行多个docker容易的工具, 通过Compose, 你可以用YAML文件来配置你的程序.
使用Compose基本上需要三步 :  
1. 通过Dockerfile定义你的app的环境, 这样环境就可以在任何地方随意复制了  
2. 在docker-compose.yml中定义构成你的app的service, 这样这些服务就可以在隔离的环境中一起运行了  
3. 运行命令docker-compose up, 这时候Compose就会把你的整个app拉起来了  

docker-compose.yml看起来应该是这样子的 :  
```
version: '3'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```