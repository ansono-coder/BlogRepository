---
title : mybatis框架学习阶段（六）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## mybatis 参数传递问题详解
### 1.SQL语句的标签中的parameterType属性
- 用来指定传入sql语句中的参数的类型，一般很少用
### 2.单参数传递
- 基本类可以直接传递不指定parameterType的值
  - byte,short,int,long,float,double,boolean,char
  - String,Date
### 3.多参数传递
- 不使用param注解
  - sql语句中的#{}中参数名从arg0开始或者从param1开始
- 使用param注解
  - 指定#{}中的参数名
  ```java
    //@param(value="xxx"),value可以省略不写
    public int selectCarByPriceAndCarType(@param("price")Decimal price,@param("carType")String carType) ;
  ```
### 到此，本文结束，记录完成