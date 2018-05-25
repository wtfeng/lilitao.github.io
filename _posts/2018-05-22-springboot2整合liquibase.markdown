---
layout: post
title: springBoot2整合liquibase
date: 2018-05-22
categories: springboot
---
<p>
Spring boot2整合liquibase
spring boot2内置即已经支持liquibase,在pom.xml文件加入liquibase依赖：
</p>

    <dependency>
           <groupId>org.liquibase</groupId>
           <artifactId>liquibase-core</artifactId>
    </dependency>

<p>
在application.properties文件加入liquibase配置项
</p>

    spring.liquibase.enabled=true
    spring.liquibase.drop-first=false
    spring.liquibase.change-log=classpath:changelog.master.xml
    spring.liquibase.user=sa
    spring.liquibase.password=
    spring.liquibase.url=jdbc:h2:file:~/test
<p>
由于使用了h2内存数据库，所以在pom.xml文件中加入h2的依赖
</p>

    <dependency>
        <groupId>com.h2database</groupId>
         <artifactId>h2</artifactId>
    </dependency>
<p>
启动SpringApplication的main方法，但是在输出的日志中找不到关于liuquibase启动的相关日志，查找日志发现有一行输出：
</p>

    The Class-Path manifest attribute in C:\Users\admin\.m2\repository\org\liquibase\liquibase-core\3.5.5\liquibase-core-3.5.5.jar referenced one or more files that do not exist: file:/C:/Users/admin/.m2/repository/org/liquibase/liquibase-core/3.5.5/lib/snakeyaml-1.13.jar

<p>
暂时不清楚什么原因maven没有自动下载到snkeyaml-1.13.jar文件，手动到maven中央repository下载这个jar包并放到指定目录下或者手动指定liquibase为最新的版本
</p>

       <dependency>
            <groupId>org.liquibase</groupId>
            <artifactId>liquibase-core</artifactId>
            <version>3.6.1</version>
        </dependency>

<p>经过以上步骤还是不动自动启动liuqibase，经调试发现，pom.xml少了jdbc的依赖，加入以下依赖：</p>
      <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
<p>
后启动springboot,在启动日志中找到关于liquibase的启动信息，但是大部分情况下，除了db unit test，我们不希望每次启动spring boot都启动liquibase和h2，并执行数据库的ddl语句,我们也不把上面的配置信息带上product，product不需要使用h2,也不需每次启动都部署db或者验证我们的db ddl语句，那么我们在什么情况下需要用到以上的配置呢！我们只希望在启动db unit test时执行以上配置，或者利用liuqibase和h2验证我们的持续开发与演化的db constructure ,除了我们本机执行这种测试我们也可以把这些集成到CI服务器上定时运行，保证我们的持续开发集成的正确性。所以上面h2 和liquibase的dependency的scope，我们应该设置为test
</p>
<p>
我们除了用liquibase做db test之外，对于product或者uat还有持续交付集成的功能，当我们的开发成果当中包括有db constructure时，比如：随着开发的推进，我们的新增了表，或者某个表新增了字段，删除字段等等，此类db的开发交付，我们可以交给liquibase来管理，当我们的某次交付过程中包括有db的开发成果时，我们单独运行一关于liquibase命令直接把开发成果部署到uat或者product，liquibase负责管理两者之间的版本差异和冲突解决方案，或者对于一个严谨的，重要的项目来说，直接使用一个自动化的工具处理db是十分危险的，这时我们可以使用liquibase来管理product或者uat成开发之间的版本差异与冲突时，通过对比生成增量部署的原生sql语句，再让有经验的相关人员确认后，再执行这份执行计划。
</p>
<p>
既然上面的配置是针对持续开发集成的，以下配置让我们有效地管理我们的持续交付，在parent的pom.xml中加入liquibase的插件管理配置：
</p>

    <build>
    <pluginManagement>

      <plugins>
          <plugin>
              <groupId>org.liquibase</groupId>
              <artifactId>liquibase-maven-plugin</artifactId>
          </plugin>
      </plugins>
    </pluginManagement>
    </build>

<p>
然后在repository相关的项目中加入插件
</p>


    <build>
        <plugins>
            <plugin>
                <groupId>org.liquibase</groupId>
                <artifactId>liquibase-maven-plugin</artifactId>
                <configuration>
                    <propertyFile>target/classes/liquibase.properties</propertyFile>
                    <promptOnNonLocalDatabase>false</promptOnNonLocalDatabase>
                </configuration>           
            </plugin>

        </plugins>
    </build>

如果你在执行中遇到以下错:

	[ERROR] Failed to execute goal org.liquibase:liquibase-maven-plugin:3.0.5:update (default) on project sample.springboot.jpa: Error setting up or running Liquibase: Error parsing line 7 column 76 of db.changelog-master.xml: cvc-elt.1: 找不到元素 'databaseChangeLog' 的声明。 -> [Help 1]
    [ERROR] 
    [ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
    [ERROR] Re-run Maven using the -X switch to enable full debug logging.
    [ERROR] 
    [ERROR] For more information about the errors and possible solutions, please read the following articles:
    [ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
 

<p>
有可能是你在parent的pom.xml里加入插件的依赖管理时，加入了version,这可能是spring boot内置的版本管理造成的冲突，如下所示：
</p>

    <plugin>
          <groupId>org.liquibase</groupId>
          <artifactId>liquibase-maven-plugin</artifactId>
          <version>3.0.5</version>
    </plugin>

