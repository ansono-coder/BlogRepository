---
title : mybatis框架学习阶段（七）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## mybatis查询问题详解
### 1.返回类型
- 单个pojo类型
  - 基本操作，这里不再赘述
- List集合
  - resultType的值是pojo类，但在方法接口中返回值写List
- Map集合
  - 将值存到map集合中，key为数据库表的列名
- Map中的map集合
  - map的key是返回记录的主键值，value是一个map，键是数据库表的列名，值是数据
### 2.返回结果的映射
- 驼峰命名的自动映射机制
  - 这种方式在工作中并不常用，条件太苛刻
  >条件：数据库表的列名中单词都是小写字母，且单词与单词之间用“_”连接&&pojo类中的字段名一定要符合驼峰命名规范
  >
  - 使用方法：在settings中添加setting标签，name="mapUnderscoreToCamelCase ",value="true"
- resultMap标签
  - 通过resultType标签可以避免在sql的查询语句中起大量的字段别名
  ```xml
    <resultMap id="selectCarMap" type="Car">
        <!--这个一定要加上去，目的是为了提高mybatis的效率-->
        <id property="id" column="id"/>
        <result property="carName" column="car_name"/>
        <result property="guidePrice" column="guide_price"/>
        <result property="createTime" column="create_time"/>
        <result property="carType" column="car_type"/>
    </resultMap>
    <select id="selectOne" resultMap="selectCarMap" >
        select id ,car_name ,guide_price ,create_time ,car_type from cars where id = #{id}
    </select>
  ```

### 到此，本文结束，记录完成