---
title:  学习 MyBatis
date: 
categories:
- 数据库
tags:
- ORM
---

### 传统 JDBC 程序的设计缺陷

* 大量配置信息硬编码，数据库密码、用户等
* 大量的无关业务处理的编码
* 扩展优化极为不便





### MyBatis 概述

MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架。

* 避免传统 JDBC 硬编码
* XML 配置或者注解
* POJO对象和数据库记录直接映射
* 完善的文档支持



### MyBatis 的使用优势

* 数据库交互信息配置化

* 动态 SQL 处理（一级缓存、二级缓存）



### MyBatis 的使用场景

* 更加关注 SQL 优化的项目
* 需求频繁更新改动的项目



### MyBatis 核心 API 操作过程分解

* SQL输入参数  +  去数据库中操作
* 数据库操作
  * mybatis 配置
    * mybatis.xml 主配置文件
    * XXXMapper.xml 映射配置文件
  * 构建和数据库之间的对话
    * SqlSessionFactoryBuilder => SqlSessionFactory => SqlSession =>Executor => Mapped Statement
  * 操作数据库
* SQL 输出结果集



### MyBatis 架构分解

* 接口层
  * 数据查询接口
  * 数据新增接口
  * 数据更新接口
  * 数据删除接口
  * 获取配置接口
* 数据处理层
  * 参数映射
    * 参数映射配置
    * 参数映射解析
    * 参数类型解析
  * SQL 解析
    * SQL 获取
    * SQL 解析
    * 动态 SQL
  * SQL 执行
    * SimpleExecutor
    * BatchExecutor
    * ReuseExecutor
  * 结果映射
    * 结果映射配置
    * 结果类型转换
    * 结果数据拷贝
* 基础支撑层
  * 配置框架
    * 连接管理
    * 事务管理
    * 配置加载
    * 缓存处理



### 使用

动态 sql

数据库和实体类不一致，resultMap 而不用 resultType



log4j 日志管理 debug 级别 info 级别