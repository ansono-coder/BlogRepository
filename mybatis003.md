---
title : mybatis框架学习阶段（三）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## 关于mybatis框架中mybatis-config.xml文件的配置详解
### 列出整个基础的mybatis-config.xml文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--这个是mybatis官方的scheme约束文件，告诉我们这个xml文件的配置规则-->
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--environments中的每一个environment标签其实就是一个数据库的配置信息-->
    <!--default属性的值写environment标签id属性的值，表明在java程序中如果没有制定采用环境，则默认将该环境作为要操作的环境-->
    <environments default="development">
        <environment id="development">
        <!--事务管理，存在两个值：JDBC|MANAGED，前者表示使用mybatis管理事务，后者表示使用其他容器管理，如spring..-->
            <transactionManager type="JDBC"/>
        <!--这里的dataSource指的是数据源，用来获取connection对象的，type="[UNPOOLED|POOLED|JNDI]-->
        <!--UNPOOLED:不开启数据库连接池；POOLED：开启mybatis自带的数据库连接池；JNDI：集成第三方的数据库连接池，这些连接池都实现了JNDI规范。不同的选择，配置也不同-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="yt1226"/>
            </dataSource>
        </environment>
    </environments>
    <!--一个数据库表一个操作映射文件-->
    <mappers>
        <mapper resource="CarsMapper.xml"/>
    </mappers>
</configuration>
```
>更多的相关信息参考[mybatis中文网](https://mybatis.net.cn/)
>
### 到此，本文结束，记录完成