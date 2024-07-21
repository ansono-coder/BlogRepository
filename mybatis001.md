---
title : mybatis框架学习阶段（一）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## 1.什么是mybatis
- mybatis是一个封装了JDBC的ORM框架，主要针对的是开发过程中对数据库的操作
- mybatis就是一个jar文件，通过maven引入就完成了mybatis的下载
## 2.mybatis框架使用前的基础配置
**注意：创建的所有资源文件的查找是从根路径resouces目录下开始的**
- 在resources目录下创建"mybatis-config.xml",并编写以下内容
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="这里写Driver类的类名"/>
        <property name="url" value="这里和JDBC一样"/>
        <property name="username" value="数据库登录名"/>
        <property name="password" value="登陆密码"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```
- 对要操作的数据库表，在resouces目录下创建一个对应的mapper文件，以下以"CarsMapper.xml"为例
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cars">
    <!--这里写sql语句,这个是例子-->
    <insert id="insert-car">
        insert into cars (id,car_name,guide_price,create_time,car_type)values(null,"东风",10.0,2019-01-01,"普通车")
    </insert>

</mapper>
```
- 在mybaits-config.xml的mappers标签中添加一个mapper标签
```xml
<mappers>
  <mapper resource="CarsMapper"/>
</mappers>
```
## 3.写通过mybatis操作数据库的代码
```java
package com.ansono.mybatis;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;

public class Main {
    public static void main(String[] args) throws IOException {
        //先创建SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        //通过SqlSessionFactoryBuilder对象创建SqlSessionFactory,将"mybatis-config.xml"文件通过流的方式传入
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        //通过拿到的sqlSessionFactory开启一个sqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //sqlSession执行sql语句
        int count = sqlSession.insert("insert-car");
        System.out.println(count);
        //注意：mybatis中sql语句的提交是手动的，也就是说，mybatis中的事务是默认开启的
        sqlSession.commit();
        //记得关闭会话，节省资源
        sqlSession.close();


    }
}

```
### 到此，本文结束，记录完成
