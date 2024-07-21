---
title : mybatis框架学习阶段（九）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## mybatis高级映射
 >mybatis高级映射指的是多表操作，比如多对一或者一对多
 >
 ### 1.多对一
- 多种方式，常见的包括三种：
  - 第一种方式：一条SQL语句，级联属性映射。
  ```xml

    <resultMap id="studentResultMap" type="Student">
        <id property="sid" column="sid"/>
        <result property="sname" column="sname"/>
        <result property="clazz.cid" column="cid"/>
        <result property="clazz.cname" column="cname"/>
    </resultMap>

    <select id="selectBySid" resultMap="studentResultMap">
        select s.*, c.* from t_student s join t_clazz c on s.cid = c.cid where sid = #{sid}
    </select>
  ```
  ```java
    @Test
    public void testSelectBySid(){
        StudentMapper mapper = SqlSessionUtil.openSession().getMapper(StudentMapper.class);
        Student student = mapper.selectBySid(1);
        System.out.println(student);
    }
  ```
  - 第二种方式：一条SQL语句，association。
    - 只需要改动resultMap标签 的内容
  ```xml
  <resultMap id="studentResultMap" type="Student">
    <id property="sid" column="sid"/>
    <result property="sname" column="sname"/>
    <association property="clazz" javaType="Clazz">
        <id property="cid" column="cid"/>
        <result property="cname" column="cname"/>
    </association>
  </resultMap>
  ```
  ```java
    //这里的代码与上面的没有什么区别
  ```
  - 第三种方式：两条SQL语句，分步查询。（这种方式常用：优点一是可复用。优点二是支持懒加载。）
    - 其他位置不需要修改，只需要修改以及添加以下三处：
      - 第一处：association中select位置填写sqlId。sqlId=namespace+id。其中column属性作为这条子sql语句的条件。
        ```xml
        <resultMap id="studentResultMap" type="Student">
            <id property="sid" column="sid"/>
            <result property="sname" column="sname"/>
            <association property="clazz"
               select="com.powernode.mybatis.mapper.ClazzMapper.selectByCid"
               column="cid"/>
        </resultMap>

        <select id="selectBySid" resultMap="studentResultMap">
                select s.* from t_student s where sid = #{sid}
        </select>
        ```
      - 在ClazzMapper接口中添加方法(这里的方法是第二张表的操作抽象方法)
      - 第三处：在ClazzMapper.xml文件中进行配置
        ```xml
            <mapper namespace="com.powernode.mybatis.mapper.ClazzMapper">
                <select id="selectByCid" resultType="Clazz">
                select * from t_clazz where cid = #{cid}
                </select>
            </mapper>
        ```
  - 这种方式的优点：
    - 代码复用性增强。
    - 支持延迟加载。【暂时访问不到的数据可以先不查询。提高程序的执行效率】

 ### 2.一对多
- 一对多的实现，通常是在一的一方中有List集合属性。
- 实现的两种方式：
  - 第一种方式：collection
  ```xml
    <resultMap id="clazzResultMap" type="Clazz">
        <id property="cid" column="cid"/>
        <result property="cname" column="cname"/>
        <collection property="stus" ofType="Student">
            <id property="sid" column="sid"/>
            <result property="sname" column="sname"/>
        </collection>
    </resultMap>

    <select id="selectClazzAndStusByCid" resultMap="clazzResultMap">
        select * from t_clazz c join t_student s on c.cid = s.cid where c.cid = #{cid}
    </select>
  ```
  ```java
    @Test
    public void testSelectClazzAndStusByCid() {
        ClazzMapper mapper = SqlSessionUtil.openSession().getMapper(ClazzMapper.class);
        Clazz clazz = mapper.selectClazzAndStusByCid(1001);
        System.out.println(clazz);
    }
  ```
  - 第二种方式：分步查询
  ```xml
    <resultMap id="clazzResultMap" type="Clazz">
        <id property="cid" column="cid"/>
        <result property="cname" column="cname"/>
        <!--主要看这里-->
        <collection property="stus"
            select="com.powernode.mybatis.mapper.StudentMapper.selectByCid"
            column="cid"/>
    </resultMap>

    <!--sql语句也变化了-->
    <select id="selectClazzAndStusByCid" resultMap="clazzResultMap">
    select * from t_clazz c where c.cid = #{cid}
    </select>
  ```
  ```java
    //这里不再赘述
  ```
 ### 延迟加载
 >这个机制的目的是只在需要跨表查询的时候才跨表。无论是哪种设计的方法，都只有两种开启方式
 >
 - 第一种：fetchType="lazy"
  ```xml
    <association property="clazz"
               select="com.powernode.mybatis.mapper.ClazzMapper.selectByCid"
               column="cid"
               //开启延迟加载
               fetchType="lazy"/>
  ```
- mybatis中开启全局的延迟加载
```xml
<settings>
  <setting name="lazyLoadingEnabled" value="true"/>
</settings>
<!--开启后，去掉association语句中的fetchType属性！！-->
```
### 到此，本文结束，记录完成