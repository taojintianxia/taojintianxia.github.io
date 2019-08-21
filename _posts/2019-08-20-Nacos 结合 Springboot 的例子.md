---
layout: post  
title: Nacos 结合 Springboot 的例子  
categories: [nacos, springboot]  
description: Nacos 结合 Springboot 的例子  
keywords: nacos, springboot  
---

官方的各种 example 其实已经很清晰了，但是例子中用到了@NacosValue 标签，感觉还是有一定的侵入性，所以就根据文档的另一种方法记录了下使用 springboot 跟 spring cloud 中自带标签的方法。

### 静态配置
首先记录下静态配置的设置方式：  

##### 1. 在 resources 目录下新建application.properties，内容如下：  

```
# 开启预加载，必须
nacos.config.bootstrap.enable=true
# 主配置服务器地址
nacos.config.server-addr=http://127.0.0.1:8848
# 主配置 data-id
nacos.config.data-id=school
# 主配置 group-id
nacos.config.group=dev
# 主配置 配置文件类型
nacos.config.type=yaml
# 主配置 最大重试次数
nacos.config.max-retry=10
# 主配置 开启自动刷新
nacos.config.auto-refresh=true
# 主配置 重试时间
nacos.config.config-retry-time=3500
# 主配置 配置监听长轮询超时时间
nacos.config.config-long-poll-timeout=45000
# 主配置 开启注册监听器预加载配置服务（除非特殊业务需求，否则不推荐打开该参数）
nacos.config.enable-remote-sync-config=false
```

##### 2. 让我们新建两个配置类如下：  

```java
@Data
@Configuration
public class ClassRoomConfig {

    /**
     * 教室面积
     */
    @Value("${classroom.area:}")
    private double area;

    /**
     * 座椅数量
     */
    @Value("${classroom.chair-number:}")
    private int chairNumber;

    /**
     * 是否是老教室
     */
    @Value("${classroom.legacy:}")
    private boolean legacy;
}
```

```java
@Data
@Configuration
public class SchoolConfig {

    /**
     * 学校面积
     */
    @Value("${school.area:}")
    private double area;

    /**
     * 学校等级
     */
    @Value("${school.level:}")
    private String level;
}
```

##### 3. 新建个 controller 如下：  

```java
@Controller
@RequestMapping("school")
public class SchoolController {

    @Autowired
    private ClassRoomConfig classRoomConfig;

    @Autowired
    private SchoolConfig schoolConfig;

    @ResponseBody
    @RequestMapping(value = "/getSchool", method = GET)
    public String getSchool() {
        return "学校面积 : " + schoolConfig.getArea() + "\n学校等级 : " + schoolConfig.getLevel();
    }

    @ResponseBody
    @RequestMapping(value = "/getClassroom", method = GET)
    public String getClassroom() {
        return "教室面积 : " + classRoomConfig.getArea() + "\n教室座椅数量 : " + classRoomConfig.getChairNumber() + "\n是否是老教室 : "
                + classRoomConfig.isLegacy();
    }
}
```

到目前为止，已经万事俱备了，就差一个配置了。我们在本地启动一个 nacos 的 docker，具体教程搜一下官方网站。然后我们在 nacos 中添加数据 ： 
![](https://taojintianxia.github.io/images/posts/nacos/nacos-new-config.jpg)

Data ID : school  
Group: dev  
配置类型 : yaml  
配置内容 :   

```
# 学校的配置
school:
  area: 1200.1
  level: 永恒钻石

# 教室的配置
classroom:
  area: 88.92
  chair-number: 15
  legacy: false
```

保存完毕后，我们通过http://localhost:8080/school/getSchool 是可以访问到相应的配置的  

![](https://taojintianxia.github.io/images/posts/nacos/nacos-school.png)


### 动态配置

接下来我们记录一下如何设置使得配置内容动态更新

##### 1. 在 resources 下新建文件 bootstrap.properties，内容如下：  

```
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
spring.application.name=dynamic-school
spring.profiles.active=dev
spring.cloud.nacos.config.file-extension=yaml
```

##### 2. 创建data-id，这里要注意的是，data-id 名字的格式为：  
${prefix}-${spring.profile.active}.${file-extension}

官方文档的定定义是 ：

  - prefix 默认为 spring.application.name 的值，也可以通过配置项
  - spring.cloud.nacos.config.prefix来配置。
  - spring.profile.active 即为当前环境对应的 profile，详情可以参考 Spring Boot文档。 注意：当 spring.profile.active 为空时，对应的连接符 - 也将不存在，dataId 的拼接格式变成 ${prefix}.${file-extension}

也就是说，data-id 的名字是通过配置中的几个字段组合出来的，group 就选择 DEFAULT
_GROUP，例子中的 data-id 名字就应该是dynamic-school-dev.yaml

内容如下 ： 

```
school:
  principal: KKK
  location: HK
```

![](https://taojintianxia.github.io/images/posts/nacos/nacos-dynamic-config.png)

我们再添加个 controller：  

```java
@ResponseBody
    @RequestMapping(value = "/getDynamicSchool", method = GET)
    public String getDynamicSchool() {
        return "学校校长 : " + dynamicSchoolConfig.getPrincipal() + " \n 学校地址 : " + dynamicSchoolConfig.getLocation();
    }
```

启动浏览器访问 http://localhost:8080/school/getDynamicSchool , 我们就可以看到 DynamicSchoolConfig 的信息了

![](https://taojintianxia.github.io/images/posts/nacos/nacos-dynamic-school.png)

这时候我们修改下 nacos 中的dynamic-school-dev.yaml数据，刷新http://localhost:8080/school/getDynamicSchool ，就可以看到配置实时的更改
