---
title : mybatis框架学习阶段（二）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## 基于mybatis入门配置实现一个完整的CRUD
### 1.向数据库的cars表中增加一条记录
>这里有两种向sql语句中传参的方式，主要是CarsMapper.xml文件中sql语句处写法不同
>
- 采用Map的方式向sql语句传值
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cars">
    <!--这里写sql语句,这个是例子-->
    <insert id="insert-car">
    <!--#{写map的key}-->
        insert into cars (id,car_name,guide_price,create_time,car_type)values(null,#{car_name},#{guide_price},#{create_time},#{car_type})
    </insert>

</mapper>
```
```java
    /**
     * 通过map的方式向sql语句传参，新增数据库表中的记录
     * @throws IOException 
     */
    @Test
    public void insertTestByMap() throws IOException {
        Map<String, Object> map = new HashMap<>() ;
        map.put("car_name","法拉利") ;
        map.put("guide_price",100.0) ;
        map.put("create_time","2001-01-09") ;
        map.put("car_type","竞速车") ;
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //执行sql语句
        int count = sqlSession.insert("insert-car", map);
        System.out.println(count);
        sqlSession.commit();
        sqlSession.close();
    }
```
- 采用pojo的方式向sql语句传值
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cars">
    <!--#{这里写pojo类中字段的名称}-->
    <insert id="insert-car">
        insert into cars (id,car_name,guide_price,create_time,car_type)values(null,#{carName},#{guidePrice},#{createTime},#{carType})
    </insert>

</mapper>
```
```java
package com.ansono.pojo;

/**
 * 汽车类
 * 一个典型的pojo类
 */
public class Car {

    private Integer id ;
    private String carName ;
    private Float guidePrice ;
    private String createTime ;
    private String carType ;

    public Car(Integer id, String carName, Float guidePrice, String createTime, String carType) {
        this.id = id;
        this.carName = carName;
        this.guidePrice = guidePrice;
        this.createTime = createTime;
        this.carType = carType;
    }

    public Car() {
    }

    @Override
    public String toString() {
        return "Car{" +
                "id=" + id +
                ", carName='" + carName + '\'' +
                ", guidePrice=" + guidePrice +
                ", createTime='" + createTime + '\'' +
                ", carType='" + carType + '\'' +
                '}';
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public void setCarName(String carName) {
        this.carName = carName;
    }

    public void setGuidePrice(Float guidePrice) {
        this.guidePrice = guidePrice;
    }

    public void setCreateTime(String createTime) {
        this.createTime = createTime;
    }

    public void setCarType(String carType) {
        this.carType = carType;
    }

    public Integer getId() {
        return id;
    }

    public String getCarName() {
        return carName;
    }

    public Float getGuidePrice() {
        return guidePrice;
    }

    public String getCreateTime() {
        return createTime;
    }

    public String getCarType() {
        return carType;
    }
}

```
```java
    /**
     * 通过pojo类的方式向sql语句传参，新增数据库表中的记录
     * @throws IOException
     */
    @Test
    public void insertTestByPojo() throws IOException {
        Car car = new Car(null,"宝马",60.0f,"2017-09-11","普通车") ;
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //执行sql语句
        int count = sqlSession.insert("insert-car", car);
        System.out.println(count);
        sqlSession.commit();
        sqlSession.close();
    }
```
### 2.从数据库中删除一条记录
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cars">
    <!--#{这里写的东西和上面一样，我这里写的是变量名}-->
    <!--#{老师说这里面的东西在删除语句中可以不写，但我不写时，会报错！！}-->
    <delete id="delete-car">
        delete from cars where id=#{deleteId}
    </delete>
</mapper>
```
```java
    /**
     * 通过id来删除数据库表中的记录
     * @throws IOException
     */
    @Test
    public void deleteTest() throws IOException {
        //假设从前端拿到的要删除的记录的id是2
        int deleteId = 2 ;
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        SqlSession sqlSession = sqlSessionFactory.openSession();
        int count = sqlSession.delete("delete-car", deleteId);
        System.out.println(count);
        sqlSession.commit();
        sqlSession.close();
    }
```
### 3.从数据库表中修改一条记录
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cars">
    <!--#{写法相同}-->
    <update id="update-car">
        update cars set guide_price=#{guidePrice} where id=#{id}
    </update>

</mapper>
```
```java
    /**
     * 通过pojo修改数据库表中的记录，使用map步骤类似
     * @throws IOException
     */
    @Test
    public void updateTest() throws IOException {
        //假设要修改的记录为[id=6,guide_price=60.0f]的宝马
        int id = 6 ;
        String carType = "跑车" ;
        Car car = new Car() ;
        car.setGuidePrice(88.0f);
        car.setId(id);
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        SqlSession sqlSession = sqlSessionFactory.openSession();
        int count = sqlSession.update("update-car", car);
        System.out.println(count);
        sqlSession.commit();
        sqlSession.close();

    }
```
### 4.从数据库表中查询记录
>注意：查询的sql所在的标签要添加**returnType**属性，一般取值为pojo类的类名；查询的sql语句中要**使用as将数据库表中的列名映射成pojo类的字段名**！！！
>
- 查询单条记录
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cars">
    <!--这里的resultType写的是pojo类的名称-->
    <select id="select-one" resultType="com.ansono.pojo.Car">
        select id as id,car_name as CarName,guide_price as guidePrice,create_time as createTime,car_type as carType from cars where id=#{id}
    </select>
</mapper>
```
```java
    /**
     * 通过id查询一条数据库中的记录
     * @throws IOException
     */
    @Test
    public void selectOneTest() throws IOException {
        //假设要查询的数据的id=5
        int id = 5 ;
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        SqlSession sqlSession = sqlSessionFactory.openSession();
        Object o = sqlSession.selectOne("select-one",id);
        if (o == null) {
            new Exception().printStackTrace();
        }
        System.out.println(o);
        sqlSession.commit();
        sqlSession.close();

    }
```
- 查询所有记录
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cars">
    
    <select id="select-all" resultType="com.ansono.pojo.Car">
        select id as id,car_name as CarName,guide_price as guidePrice,create_time as createTime,car_type as carType from cars
    </select>

</mapper>
```
```java
    /**
     * 查询表中所有的数据
     * @throws IOException
     */
    @Test
    public void selectAll() throws IOException {
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        SqlSession sqlSession = sqlSessionFactory.openSession();
        List<Object> carList = sqlSession.selectList("select-all");
        if (carList == null) {
            new Exception().printStackTrace();
        }
        carList.forEach(car -> System.out.println(car));
        sqlSession.commit();
        sqlSession.close();

    }
```
### 到此，本文结束，记录完成