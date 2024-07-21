---
title : mybatis框架学习阶段（四）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## mybatis使用的一些技巧
### 1.${}和#{}的区别和相对应的一些应用场景
- 底层原理：Statement和PrepareStatement
  - #{}在mybatis的底层是使用了JDBC中的PrepareStatement，将{}中的值传入问号
  - ${}则使用的Statement，只将值以字符串的形式传递给sql语句的位置
- ${}应用场景
  - 拼接表名
  ```xml
    <insert id='insert'>
        insert into ${tableName} (x,x,x,x,x)values(y,y,y,y,y) 
    </insert>
  ```
  - 批量删除
  ```xml
    <delete id='delete'>
        delete from t_user where id in(${ids})
    </delete>
  ```
  - 模糊查询
  ```xml
    <select id='select' resultType='xxx'>
        select * from t_user where brand like '%${userName}%'
    </select>
  ```
### 2.resultType起别名
- 为什么要起别名？
  - 如果要使用mybatis的getMapper方法，就一定要将resultType的返回值写成pojo中相关类的全限定包名，然而，每次都这么写会非常地烦
  - 如果写错了，就会更加地烦
- 怎么做?
  - 第一种方法
  ```xml
    <!--优点：在sql语句的书写时resultType会方便一些；缺点：n个pojo类，就有n条语句-->
    <configuration>
        <typeAliases>
            <!--alias可以不写，默认pojo.后面的就是别名-->
            <typeAlias type="com.xx.xx.pojo.Xxx" alias="Xxx" />
        </typeAliases>
    </configuration>
  ```
  - 第二种方法
  ```xml
    <!--推荐用这种！！！-->
    <configuration>
        <!--表示将这个包下所有的类在resultType中起别名，别名就是类的简类名-->
        <package name='com.xx.xx.pojo'>
    </configuration>
  ```
### 2.mapper属性的配置
>小提示：java目录和resources目录下都是根路径
>
- 三种配置方法
  - resource
  ```xml
    <!--比较常用的方法，resource目录下开始查找-->
    <mapper resource="xxx.xml"/>
  ```
  - url
  ```xml
    <!--不推荐使用的方法，绝对路径，兼容性太差-->
    <mapper url="file://d/dev/xxx.xml"/>
  ```
  - class
  ```xml
    <!--在resources目录下创建多级目录com/xxx/xxx/mapper,多级目录名称和存放表操作方法接口的包名一致，并将这个mapper.xml文件移动到新创建的目录下-->
    <mapper class="com.xxx.xxx.mapper.XxxMaper"/>
  ```
- 简化配置法
```xml
    <!--条件：所有的mapper.xml与接口放在一起（其实就是放在resources目录的与包同名的多级目录下），且xml文件名和接口名一致-->
    <!--这种方式较为常用-->
    <mappers>
        <package name="com.xxx.xxx.mapper"/>
    </mappers>
```
### 3.idea模板文件的配置
- 配置模板文件是为了日后创建项目时不再手动编写一些配置文件的通用项
- 配置步骤
  - 点击**File**->选择**Editor**->在其下找到**File and Code Templates**并选中，点击**加号**
  - 光标闪动的框框就是写模板代码的地方，**Name**属性的值就是创建文件时的查找标识，**File name**就是文件创建后的文件的名字
  - 完成后，点击**Apply**
### 4.在insert的SQL语句中获得返回的主键
- 存在这样的业务，通过获取返回的主键判断新增记录的用户是第几位，比如，第1000位用户会获得vip优惠卡等
- 实现步骤
  - 在insert语句中添加属性
  ```xml
    <!--useGeneratedKeys属性的值表示是否返回表中的id值，keyProperty的值表示返回后用pojo类的哪个字段去接收-->
    <insert id='insertOne' useGeneratedKeys="true" keyProperty="id">
  ```
  ```java
    //通过pojo类的get方法获取新增记录在表中的id
     Integer id = car.getId();
  ```
  ### 到此，本文结束，记录完成