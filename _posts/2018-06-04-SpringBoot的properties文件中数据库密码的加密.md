---
layout: post  
title: template page  
categories: [SpringBoot, jasypt, 加密]  
description: some word here  
keywords: jasypt, 数据库密码, 加密  
---

#### 目前大部分Spring Boot的项目中对各种资源的连接基本都是明文用户名/密码的, 类似于如下结构 :
```
#mybatis
spring.datasource.driverClassName = com.mysql.jdbc.Driver
spring.datasource.url = jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8
spring.datasource.username = root
spring.datasource.password = root
```

#### 这种配置虽然很方便, 但是各种密码都是明文的, 而且密码跟着代码发布, 很不安全.