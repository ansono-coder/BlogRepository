---
title : mybatis框架学习阶段（十）
categories : 编程技术
tags : 
    - java
    - mybatis
---
## mybatis缓存
### 1.缓存介绍
- 这里的缓存主要针对的是查询后结果的缓存
- 目的是为了执行同一条sql查询语句时不需要重复连接数据库操作,同时减少复杂的查询算法的多次执行
- 三种缓存模式：一级缓存、二级缓存、第三方集成缓存
### 2.一级缓存
- 使用说明：
  - 默认开启，无需配置
  - 原理：只要使用同一个SqlSession对象执行同一条SQL语句，就会走缓存
  - 注意：如果编写了关联了ThreadLocal的SqlSessionUtil工具类，通过它获取的SqlSession对象是与当前线程绑定的
- 不走缓存的情况：
  - 第一种：不同的SqlSession对象
  - sql查询的语句不同
- 实效的情况：
  - 第一次查询和第二次查询之间，手动清空了一级缓存
  ```java
    sqlSession.clearCache();
  ```
  - 第一次查询和第二次查询之间，执行了增删改操作。【这个增删改和哪张表没有关系，只要有insert delete update操作，一级缓存就失效】
### 3. 二级缓存
- 使用说明：
  - 二级缓存的范围是SqlSessionFactory
  - 使用二级缓存需要满足4个条件：
    - 《setting name="cacheEnabled" value="true"》 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。默认就是true，无需设置。
    - 在需要使用二级缓存的SqlMapper.xml文件中添加配置：《cache /》,cache标签属性的详解如下：
      - eviction:
        1. LRU：Least Recently Used。最近最少使用。优先淘汰在间隔时间内使用频率最低的对象。(其实还有一种淘汰算法LFU，最不常用。)
        2. FIFO：First In First Out。一种先进先出的数据缓存器。先进入二级缓存的对象最先被淘汰。  
        3. SOFT：软引用。淘汰软引用指向的对象。具体算法和JVM的垃圾回收算法有关。
        4. WEAK：弱引用。淘汰弱引用指向的对象。具体算法和JVM的垃圾回收算法有关。
      - flushInterval：
        1.   二级缓存的刷新时间间隔。单位毫秒。如果没有设置。就代表不刷新缓存，只要内存足够大，一直会向二级缓存中缓存数据。除非执行了增删改。
      - readOnly：
        1. true：多条相同的sql语句执行之后返回的对象是共享的同一个。性能好。但是多线程并发可能会存在安全问题。
        2. false：多条相同的sql语句执行之后返回的对象是副本，调用了clone方法。性能一般。但安全。
      - size：
        1. 设置二级缓存中最多可存储的java对象数量。默认值1024。
    - 使用二级缓存的实体类对象必须是可序列化的，也就是必须实现java.io.Serializable接口
    - SqlSession对象关闭或提交之后，一级缓存中的数据才会被写入到二级缓存当中。此时二级缓存才可用。
### 4. 集成的第三方缓存
- 有哪些第三方缓存:
  - EhCache(java开发的，比较常用)
  - Memcache（C语言开发的）
  - ...
- 需要注意的地方
  - 集成EhCache是为了代替mybatis自带的二级缓存。一级缓存是无法替代的
- 集成EhCache相关步骤：
  1. 引入mybatis整合ehcache的依赖
   ```xml
    <!--mybatis在pom.xml中集成ehcache的组件-->
    <dependency>
        <groupId>org.mybatis.caches</groupId>
        <artifactId>mybatis-ehcache</artifactId>
        <version>1.2.2</version>
    </dependency>
   ```
  2. 在类的根路径下新建echcache.xml文件，并提供以下配置信息。
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
    <!--磁盘存储:将缓存中暂时不使用的对象,转移到硬盘,类似于Windows系统的虚拟内存-->
    <diskStore path="e:/ehcache"/>
  
    <!--defaultCache：默认的管理策略-->
    <!--eternal：设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效，如果为false那么还要根据timeToIdleSeconds，timeToLiveSeconds判断-->
    <!--maxElementsInMemory：在内存中缓存的element的最大数目-->
    <!--overflowToDisk：如果内存中数据超过内存限制，是否要缓存到磁盘上-->
    <!--diskPersistent：是否在磁盘上持久化。指重启jvm后，数据是否有效。默认为false-->
    <!--timeToIdleSeconds：对象空闲时间(单位：秒)，指对象在多长时间没有被访问就会失效。只对eternal为false的有效。默认值0，表示一直可以访问-->
    <!--timeToLiveSeconds：对象存活时间(单位：秒)，指对象从创建到失效所需要的时间。只对eternal为false的有效。默认值0，表示一直可以访问-->
    <!--memoryStoreEvictionPolicy：缓存的3 种清空策略-->
    <!--FIFO：first in first out (先进先出)-->
    <!--LFU：Less Frequently Used (最少使用).意思是一直以来最少被使用的。缓存的元素有一个hit 属性，hit 值最小的将会被清出缓存-->
    <!--LRU：Least Recently Used(最近最少使用). (ehcache 默认值).缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存-->
    <defaultCache eternal="false" maxElementsInMemory="1000" overflowToDisk="false" diskPersistent="false"
                  timeToIdleSeconds="0" timeToLiveSeconds="600" memoryStoreEvictionPolicy="LRU"/>

   ```
  3. 修改SqlMapper.xml文件中的cache标签，添加type属性
   ```xml
    <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
   ```
  4. 编写测试程序使用


### 到此，本文结束，记录完成

