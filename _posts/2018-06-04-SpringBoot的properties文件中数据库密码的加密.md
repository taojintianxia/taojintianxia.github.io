---
layout: post  
title: SpringBoot的properties文件中数据库密码的加密  
categories: [SpringBoot, jasypt, 加密]  
description: some word here  
keywords: jasypt, 数据库密码, 加密  
---

#### 目前大部分Spring Boot的项目中对各种资源的连接基本都是明文的用户名/密码, 类似于如下结构 :
```
#mybatis
spring.datasource.driverClassName = com.mysql.jdbc.Driver
spring.datasource.url = jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8
spring.datasource.username = root
spring.datasource.password = root
```

#### 这种配置虽然很方便, 但是各种密码都是明文的, 而且密码跟着代码发布, 很不安全.简单调研后, 发现jasypt是专门加密配置文件中的密码的, 下面记录下如何使用jasypt

#### 1. 首先在maven中添加jasypt的依赖 :
```xml
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>2.0.0</version>
</dependency>
```

#### 2. 在如下地址下载jasypt :
```
下载jar版本 : 去maven的repository里找
或下载zip版本 : https://sourceforge.net/projects/jasypt/files/
```
按照 java -cp jasypt-1.9.2.jar  org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input=YOUR_PASSWORD password=YOUR_SALT algorithm=ALGORITHM 的语法去生成你的密码  
例如 :
```
java -cp jasypt-1.9.2.jar  org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input=root password=SALT_IS_GOOD algorithm=PBEWithMD5AndDES
```
要注意的是, input才是你的真正的密码,password只不过是盐.(不知道盐的同学去Google一下)

或者下载那个zip包, 按照./encrypt.sh input=YOUR_PASSWORD password=YOUR_SALT algorithm=ALGORITHM的语法生成加密的密码. 方法跟上面jar的就类似了
最后会生成如下图的结果, OUTPUT就是加密后的密码.  
![](https://taojintianxia.github.io/images/posts/springboot/jasypt/encript.png)

#### 3. 在配置文件中, 将明文密码替换掉, 并添加jasypt的盐跟算法
```
#mybatis
spring.datasource.driverClassName = com.mysql.jdbc.Driver
spring.datasource.url = jdbc:mysql://localhost/test?useUnicode=true&characterEncoding=utf-8
spring.datasource.username = root
spring.datasource.password=ENC(CByiEf4a8NhKWAiYQtOVhQ==)

# jasypt
jasypt.encryptor.password=AAA
jasypt.encryptor.algorithm=PBEWithMD5AndDES
```

#### 4. 在spring boot中,在Application上添加注释 @EnableEncryptableProperties即可














