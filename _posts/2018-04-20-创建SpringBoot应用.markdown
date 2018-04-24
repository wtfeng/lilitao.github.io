---
layout: post
title:  创建SpringBoot应用
date: 2018-04-08
categories: translate
---
原文地址：https://spring.io/guides/gs/spring-boot/

一个最基本的spring boot maven配置的pom.xml文件


{% highlight xml %}

	<?xml version="1.0" encoding="UTF-8"?>
	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-spring-boot</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

	</project>
{% endhighlight %}

spring-boot-maven-plugin提供了很多方便的功能：
* 收集classpath下面的所有jar包,然后builds一个单一的，可运行的jar包，这个jar可以方便地运行并提供服务接口
* 查找public static void main()方法，并标记不这个jar的运行入口。
* 提供一个内建的依赖解析器，这个解析器用来解定spring boot的匹配<a href="https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml">Spring Boot dependencies</a>合适版本,你可以覆盖重写指定的版本号，

创建一个spring boot启动类

src/main/java/hello/Application.java

{% highlight java %}
	package hello;
	
	import java.util.Arrays;
	
	import org.springframework.boot.CommandLineRunner;
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.annotation.Bean;
	
	@SpringBootApplication
	public class Application {
	
	    public static void main(String[] args) {
	        SpringApplication.run(Application.class, args);
	    }
	
	    @Bean
	    public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
	        return args -> {
	
	            System.out.println("Let's inspect the beans provided by Spring Boot:");
	
	            String[] beanNames = ctx.getBeanDefinitionNames();
	            Arrays.sort(beanNames);
	            for (String beanName : beanNames) {
	                System.out.println(beanName);
	            }
	
	        };
	    }
	
	}

{% endhighlight %}

@SpringBootApplication这个注解给这个类注入了以下功能：
* @Configuration 相当于加注了这个注解，标记当前类是一个类定义bean,相当于xml里的<beans>标签。
* @EnableAutoConfiguration相当于加注了这个注解，告诉spring boot扫描classpath判断需要加哪个bean和各种属性配置
* 正常情况下，作为一个spring mvc项目，你需要加@EnableWebMvc告诉spring,当前是一个spring web mvc项目，但是，这里spring boot会自动加上这个配置，当在classpath扫描到spring-webmvc的jar包，这标记当前app作为一个web app并激活web app的各种行为，比如，启动DispatcherServlet
* @ComponentScan相当于加注了这个注解，告诉spring boot在当前包及子包中扫描其他的：component、configuration、service、controll等spring定义的bean