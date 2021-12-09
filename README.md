# 重学Java
> 世界一直都在变化着，也一直不变着

2016年本科毕业至今一直从事Java开发的工作，慢慢变得越来越不会Java了，所以打算重新学习，记录我对Java的经验和感悟

## 关于我
重庆某211大学软件工程毕业，Java开发者，技术迷茫者，学习者，CRUD工程师

## Java体系(基于1.8)
> 对于一个东西一般会问：xxx是什么？有什么用？xxx的优势与劣势？xxx的使用场景（或你是怎么用的）？

这里记录我以前认识欠缺，但又经常被问到的一些知识点，所有资料和源码基于`Java1.8`

 - ### JVM与Java的内存模型
1. Java内存模型
2. JVM的堆和栈，方法区，
3. JVM的新生代老年代和持久代和所对应的GC与相关JVM命令参数
4. JVM相关命令和参数
5. Java的类加载与JVM内存（反射与类加载）

 - ### Java中的数据结构和算法
 1. String的特性，不可变的优劣势，常用方法，面试题
 2. 常用的List(ArrayList),Set(HashSet),Map(HashMap)
 3. Java中其他的数据结构，树，栈，队列
 4. Java中的数据结构与线程安全
 
 - ### Java多线程

1. 线程的状态，生命周期
2. `ThreadLocal`线程变量是什么，`Volatile`关键字怎么用？
3. 锁，`syncronized`，`lock`，`Atomic`原子类的相关用法，可重入锁与死锁
4. `java.util.concurrent`包和`Executor`相关的用法

- ### Spring框架
1. Spring如何处理一个HTTP请求（HTTP请求接收到响应全流程）?
2. spring单例bean的线程安全，bean的生命周期
3. 自定义spring boot的@Enable注解注入组件
4. 缓存模型,缓存的场景，优劣势与一些BUG


- ### Mysql
1. mysql的相关定义, 锁,索引,事务(隔离级别),数据结构,
2. mysql锁：锁类型,锁粒度，读写锁，共享排他锁，表锁行锁字段锁，乐观锁和悲观锁，MVCC的分别的理论和实现方式
3. mysql索引，联合索引，索引的数据结构和原理
4. mysql原理 B树和B+树的区别
5. mysql分表，水平分表和垂直分表，实现方案
6. mysql的主从，实现的方案，配置，与项目实例
7. mysql的explain查询计划和调优

- ### 其他框架整合Demo
1. 缓存框架Redis的概述和整合spring boot
2. Redis如何实现分布式锁
3. 消息队列框架：Kafka, RocketMQ与整合spring boot
4. Kafka的鉴权,消息拦截器,Ack机制,分组消费
5. 权限认证: Spring Security和JWT的概述和整合spring boot
6. 全文检索与日志分析：ELK概述和整合spring boot
7. Mongodb的整合与存储文件，集群和分片
8. nacos配置中心和服务注册与发现的整合
9. spring cloud alibaba相关微服务框架整合
10. 浅尝spring webflux与响应式函数式编程
11. 浅尝vert.x与spring相关的内容

## 设计与方案
- 缓存的设计
- 消息队列
- spring自定义改造
- 数据权限和功能权限的简单方案
- 解耦的利器，spring 自带的事件驱动,以及分布式的事件驱动
- 权限的延续之组织机构和角色
- 文件上传和简单方案（存储和使用）
- 简单支付系统的对接（源码）
- 摄像头的对接方案
- 简单消息系统的实现方案(源码)

## 项目实战

- 构建多模块项目Maven和Gradle的选择
- [Java实现HTML转PDF(利用IText)](doc/项目实战和Demo/Java实现HTML转PDF文档.md)
- Java实现Word转PDF预览(利用Aspose)

## BUG和记录


## 一些闲谈

