---
title : mybatis框架学习阶段（四）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## 使用动态代理的方式在mybatis中实现面向接口的CRUD
### 原因说明
>在mybatis中，尤其是使用mybatis最为持久层(DAO)的web项目中，我们都会写dao包，并将其中的方法定义在接口中，创建实现类去实现此接口，但问题在于，在这种接口中的方法一般是一些增、删、改、查方法，如果存在100张表，我们就要写100个实现类，这种情况非常不乐观，于是，采用动态代理在内存中的想法就诞生了
>
### mybatis中的getMapper(Class<T> interfaceReference)方法
>好消息：使用动态代理的这么一个思路mybatis开发人员已经写好了,但在使用前要遵守几个规则
>
1. 所有的xxxmapper.xml中namespace必须是抽象接口的全限定包名(com.xxx.xxx.xxx.XxxMapper)
2. xxxmaper.xml文件中所有的sql语句id必须和抽象接口中方法名一致(一个方法对应一个sql的id)

- 对应的xml文件显示如下
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ansono.mapper.CarMapper">

    <insert id="insertCar">
        insert into cars(id,car_name,guide_price,create_time,car_type)values(null,#{carName},#{guidePrice},#{createTime},#{carType})
    </insert>

</mapper>
```
- 对应的java代码
```java
    /**
     * 使用getMappeer(Class<T> classReference)方法动态生成CRUD接口的实现类
     * SqlSessionUtils是我个人写的工具类
     */
    @Test
    public void insertTest(){
        Car car = new Car(null,"宾利",120.0f,"2022-10-05","跑车") ;
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        int count = mapper.insertCar(car);
        System.out.println("插入的条数："+count);
        sqlSession.commit();
        //关闭sqlsession对象并移出线程
        SqlSessionUtils.close(sqlSession);

    }
```
### 底层说明
- 这个方法的底层调用了一个名为**javasist**的依赖，这个依赖专门用于在内存中生成java类的class文件
- 通过动态的方式返回需要的对象
- 这里不细说，对底层原理感兴趣的朋友可以去下面这个地址看看
>[杜老师讲解的getMapper方法底层(P59~P63)](https://www.bilibili.com/video/BV1JP4y1Z73S?p=59&vd_source=50b5bb84d5e362aa0560dd53c66fc3f8)
>
### 到此，本文结束，记录完成

