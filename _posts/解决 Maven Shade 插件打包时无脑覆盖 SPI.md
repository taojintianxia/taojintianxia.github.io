---
layout: post  
title: 解决 Maven Shade 插件打包时无脑覆盖 SPI  
categories: [maven, shade, spi]  
description: 解决 Maven Shade 插件打包时无脑覆盖 SPI  
keywords: maven, shade, spi  
---

## 现象
最近在使用 JMH 的时候，发现其将项目打包为 jar 的 shade 插件打包后的文件无法使用，缺这少那的。

排查后才发现，项目中使用了很多 SPI，而 maven 的 shade 插件在打包的时候，不会在 services 下的 spi 文件中加入所有的实现类，而是按打包顺序覆盖。

## 修复
在 pom.xml 中将不需要覆盖的 SPI 添加进去即可，格式如下：

```xml

   <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <compilerVersion>${javac.target}</compilerVersion>
                    <source>${javac.target}</source>
                    <target>${javac.target}</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.2</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <finalName>${uberjar.name}</finalName>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>org.openjdk.jmh.Main</mainClass>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/services/org.apache.shardingsphere.infra.rule.builder.scope.SchemaRuleBuilder</resource>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/services/org.apache.shardingsphere.infra.rule.checker.RuleConfigurationChecker</resource>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/services/org.apache.shardingsphere.infra.yaml.config.swapper.YamlRuleConfigurationSwapper</resource>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/services/org.apache.shardingsphere.sql.parser.spi.DatabaseTypedSQLParserFacade</resource>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/services/org.apache.shardingsphere.sql.parser.spi.SQLParserConfiguration</resource>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/services/org.apache.shardingsphere.sql.parser.spi.SQLVisitorFacade</resource>
                                </transformer>
                            </transformers>
                            <filters>
                                <filter>
                                    <!--
                                        Shading signed JARs will fail without this.
                                        http://stackoverflow.com/questions/999489/invalid-signature-file-when-attempting-to-run-a-jar
                                    -->
                                    <artifact>*:*</artifact>
                                    <excludes>
                                        <exclude>META-INF/*.SF</exclude>
                                        <exclude>META-INF/*.DSA</exclude>
                                        <exclude>META-INF/*.RSA</exclude>
                                    </excludes>
                                </filter>
                            </filters>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
        <pluginManagement>
            <plugins>
                <plugin>
                    <artifactId>maven-clean-plugin</artifactId>
                    <version>2.5</version>
                </plugin>
                <plugin>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>2.8.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-install-plugin</artifactId>
                    <version>2.5.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-jar-plugin</artifactId>
                    <version>2.4</version>
                </plugin>
                <plugin>
                    <artifactId>maven-javadoc-plugin</artifactId>
                    <version>2.9.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-resources-plugin</artifactId>
                    <version>2.6</version>
                </plugin>
                <plugin>
                    <artifactId>maven-site-plugin</artifactId>
                    <version>3.3</version>
                </plugin>
                <plugin>
                    <artifactId>maven-source-plugin</artifactId>
                    <version>2.2.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>2.17</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
```

transformer 标签对应的接口都是不要 `无脑` 覆盖的。