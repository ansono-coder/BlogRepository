---
title : mybatis框架学习阶段（八）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## 动态SQL
### 1.使用的原因
- 写死的SQL语句在实际的业务中是不存在的，尤其是查询语句
- 动态的SQL语句能帮我们更高效的处理与数据库之间的操作
### 2. 动态SQL的种类
- if案例之参数异常判断
  - 相应的xml文件
  ```xml
    <resultMap id="carResultMap" type="Car">
        <id property="id" column="id"/>
        <result property="carName" column="car_name"/>
        <result property="guidePrice" column="guide_price"/>
        <result property="createTime" column="create_time"/>
        <result property="carType" column="car_type"/>
    </resultMap>
    <select id="selectById" resultMap="carResultMap">
        <!--如果id是null或者''，就将所有结果查询出来-->
        select id , car_name , guide_price , create_time , car_type from cars where 1 = 1
        <if test="id != null and id != '' ">
            and id = #{id}
        </if>
    </select>
  ```
  - 相应的java代码
  ```java
    @Test
    public void selectById() {
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        List<Car> cars = mapper.selectById(null);
        cars.forEach(car -> {
            System.out.println(car);
        }) ;
        sqlSession.close();
    }
  ```
- where案例之多条件查询
  - 相应的xml文件
  ```xml
    <select id="selectByGuidePriceOrCarType" resultMap="carResultMap">
        select id , car_name , guide_price , create_time , car_type from cars
        <where>
            <if test="guidePrice != null and guidePrice != ''">
                and guide_price > #{guidePrice}
            </if>
            <if test="carType != null and carType !=''">
                and car_type = #{carType}
            </if>
        </where>
    </select>
  ```
  - 相应的java代码
  ```java
    @Test
    public void selectByGuidePriceOrCarType() {
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        List<Car> cars = mapper.selectByGuidePriceOrCarType(120f, "跑车");
        cars.forEach(car -> {
            System.out.println(car);
        });
        sqlSession.close();
    }
  ```
- trim案例之添加排序后缀
  - 相应的xml文件
  ```xml
    <select id="selectAllAndOrder" resultMap="carResultMap">
        select id , car_name , guide_price , create_time , car_type from cars
        <!--prefix加前缀,prefixOverrides前缀覆盖,suffix加后缀,suffixOverrides后缀覆盖-->
        <!--注意，参数的传递在后缀的拼接之前-->
        <trim prefix="order by guide_price" prefixOverrides="" suffix="" suffixOverrides="">
            <if test="target == null and target == ''">
                desc
            </if>
            <if test="target == 0">
                asc
            </if>
            <if test="target == 1">
                desc
            </if>
        </trim>
    </select>
  ```
  - 相应的java代码
  ```java
    @Test
    public void selectAllAndOrder() {
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        //传入1表示按照guide_price从大到小排，0表示从小到大排
        List<Car> cars = mapper.selectAllAndOrder(0);
        cars.forEach(car -> {
            System.out.println(car);
        });
        sqlSession.close();
    }
  ```
- set案例之根据id更新记录(这个标签主要用在update语句中)
  - 相应的xml文件
  ```xml
    <update id="updateCar">
        update cars
        <set>
            <if test="car.carName !=null and car.carName !=''">car_name = #{car.carName},</if>
            <if test="car.guidePrice !=null and car.guidePrice !=''">guide_price = #{car.guidePrice},</if>
            <if test="car.createTime !=null and car.createTime !=''">create_time = #{car.createTime},</if>
            <if test="car.carType !=null and car.carType !=''">car_type = #{car.carType},</if>
        </set>
        where id = #{car.id}
    </update>
  ```
  - 相关的java代码
  ```java
    @Test
    public void updateCar() {
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        Car car = new Car(16,"大众",16f,"2020-10-15","普通车") ;
        int count = mapper.updateCar(car);
        System.out.println(count);
        sqlSession.commit();
        sqlSession.close();

    }
  ```
- choose when otherwise案例之多条件择一查询
  - 相关xml文件
  ```xml
    <select id="selectByOneChoice" resultMap="carResultMap">
        select id , car_name , guide_price , create_time , car_type from cars
        <where>
            <choose>
                <when test="carName != null and carName != ''">car_name = #{carName}</when>
                <when test="guidePrice != null and guidePrice != ''">guide_price > #{guidePrice}</when>
                <when test="carType != null and carType != ''">car_type = #{carType}</when>
                <otherwise>
                    1=1
                </otherwise>
            </choose>
        </where>
    </select>
  ```
  - 相关的java代码
  ```java
    @Test
    public void selectByOneChoice() {
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        List<Car> cars = mapper.selectByOneChoice(null, null, null);
        cars.forEach(car -> {
            System.out.println(car);
        });
        sqlSession.close();
    }
  ```
- foreach案例之批量新增
  - 相关的xml文件
  ```xml
    <insert id="insertSome">
        insert into cars values
        <foreach collection="cars" item="car" separator=",">
            (null,#{car.carName},#{car.guidePrice},#{car.createTime},#{car.carType})
        </foreach>
    </insert>
  ```
  - 相关的java代码
  ```java
    @Test
    public void insertSome() {
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        List<Car> cars = new ArrayList<>();
        Car car1 = new Car(null, "东风雪铁龙1", 40f, "2019-01-09", "普通车");
        Car car2 = new Car(null, "东风雪铁龙2", 40f, "2019-02-19", "普通车");
        Car car3 = new Car(null, "东风雪铁龙3", 40f, "2019-09-29", "普通车");
        cars.add(car1);
        cars.add(car2);
        cars.add(car3);
        mapper.insertSome(cars) ;
        sqlSession.commit();
        sqlSession.close();
    }
  ```
### 3.sql标签和include标签
- 目的是为了减少重复代码的编写次数,但这个不常用，作为了解
- 用法如下:
```xml
    <sql id="selectUse">id , car_name , guide_price , create_time , car_type</sql>
    
    <select id="selectBySqlAndInclude" resultMap="carResultMap">
        select <include refid="selectUse"/> from cars ;
    </select>
```
- java代码如下：
```java
    @Test
    public void selectBySqlAndInclude() {
        SqlSession sqlSession = SqlSessionUtils.openSession();
        CarMapper mapper = sqlSession.getMapper(CarMapper.class);
        List<Car> cars = mapper.selectBySqlAndInclude();
        cars.forEach(car -> {
            System.out.println(car);
        });
        sqlSession.close();
    }
```
### 到此，本文结束，记录完成