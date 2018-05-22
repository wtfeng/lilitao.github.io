---
layout: post
title: springBoot2整合liquibase
date: 2018-05-22
categories: springboot
---
Spring boot2整合liquibase
spring boot2内置即已经支持liquibase,在pom.xml文件加入liquibase依赖：
    <dependency>
           <groupId>org.liquibase</groupId>
           <artifactId>liquibase-core</artifactId>
    </dependency>
在application.properties文件加入liquibase配置项
    spring.liquibase.enabled=true
    spring.liquibase.drop-first=false
    spring.liquibase.change-log=classpath:changelog.master.xml
    spring.liquibase.user=sa
    spring.liquibase.password=
    spring.liquibase.url=jdbc:h2:file:~/test
由于使用了h2内存数据库，所以在pom.xml文件中加入h2的依赖
    <dependency>
        <groupId>com.h2database</groupId>
         <artifactId>h2</artifactId>
    </dependency>

启动SpringApplication的main方法，但是在输出的日志中找不到关于liuquibase启动的相关日志，查找日志发现有一行输出：
    The Class-Path manifest attribute in C:\Users\admin\.m2\repository\org\liquibase\liquibase-core\3.5.5\liquibase-core-3.5.5.jar referenced one or more files that do not exist: file:/C:/Users/admin/.m2/repository/org/liquibase/liquibase-core/3.5.5/lib/snakeyaml-1.13.jar
暂时不清楚什么原因maven没有自动下载到snkeyaml-1.13.jar文件，手动到maven中央repository下载这个jar包并放到指定目录下或者手动指定liquibase为最新的版本
       <dependency>
            <groupId>org.liquibase</groupId>
            <artifactId>liquibase-core</artifactId>
            <version>3.6.1</version>
        </dependency>

经过以上步骤还是不动自动启动liuqibase，经调试发现，pom.xml少了jdbc的依赖，加入以下依赖：
      <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
后启动springboot,在启动日志中找到关于liquibase的启动信息