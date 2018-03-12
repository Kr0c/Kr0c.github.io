---
layout: post
title: 代码生成神器：MyBatis Generator的使用
tags: Java
---

# 一、简介
MyBatis Generator（MBG）是MyBatis MyBatis和iBATIS的代码生成器。 它支持所有版本的MyBatis以及2.2.0版本之后的iBATIS。 它通过查询数据库表，逆向生成可用于访问表的代码（POJO, DAO层接口类和mapper）。 这大大减少了设置对象和配置文件与数据库表进行交互的工作量。 MBG旨在对简单的CRUD操作（创建，检索，更新，删除）自动生成代码。 换句话说，你仍然需要为其他复杂的数据库操作手动编写SQL代码。

# 二、使用
MyBatis Generator有很多种运行方式，如Java代码，基于Maven/Eclipse插件等等。本文以Maven插件为例，此方法用法非常简便。

## 1.建立Maven工程
首先建立一个Maven工程，修改pom.xml文件：

```XML
<dependencies>
    <dependency>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-core</artifactId>
        <version>1.3.5</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.5</version>
            <configuration>
                <!--default path-->
                <configurationFile>${basedir}/src/main/resources/generatorConfig.xml</configurationFile>
            </configuration>
            <executions>
                <execution>
                    <id>Generate MyBatis Artifacts</id>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## 2.编写配置文件
在src/main/resources目录下（默认路径）新建generatorConfig.xml配置文件，并填写以下内容：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <classPathEntry location="D:\m2\repository\mysql\mysql-connector-java\5.1.30\mysql-connector-java-5.1.30.jar"/>

    <!--targetRuntime: MyBatis3 or MyBatis3Simple-->
    <context id="mybatis-generator" targetRuntime="MyBatis3" defaultModelType="flat">
        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--JDBC Connection-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://192.168.1.1:3306/database"
                        userId="root"
                        password="password">
        </jdbcConnection>

        <!--Bean-->
        <javaModelGenerator targetPackage="com.jiapengcs.bean" targetProject="src/main/java">
            <!--enableSubPackages: package.schema.XXX.java-->
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!--Mapper-->
        <sqlMapGenerator targetPackage="com.jiapengcs.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <!--DAO
            type: ANNOTATEDMAPPER, MIXEDMAPPER or XMLMAPPER
        -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.jiapengcs.dao" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!--domainObjectName-->
        <table tableName="table_order" domainObjectName="Order" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false" />
    </context>
</generatorConfiguration>
```

需要修改的地方：
- jdbcConnection: 填写数据库驱动、地址、用户名、密码
- javaModelGenerator：存放生成的POJO，如果填写的包名或者路径不存在则自动创建（下同）
- sqlMapGenerator：存放生成的Mapper
- javaClientGenerator：存放生成的DAO层接口
- table：数据库对应的表名，domainObjectName属性可以指定生成的类名（默认为表名，如tableOrder）

更多的配置内容可以参见[MBG官方文档](http://www.mybatis.org/generator/configreference/xmlconfig.html)。

## 3.生成代码
点击Maven插件中的mybatis-generator:generate：

![image](/images/mbg1.png)

只需短短1s的时间，代码便生成好了：

![image](/images/mbg2.png)

为了不浪费宝贵的生命，建议早日拥抱MyBatis Generator。

> 完整代码见Github：[mybatis-generator-demo](https://github.com/jiapengcs/mybatis-generator-demo)
