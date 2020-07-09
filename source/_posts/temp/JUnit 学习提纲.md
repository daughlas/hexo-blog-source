---
title: JUnit 学习提纲
date: 
categories:
- 临时
tags:
- 临时
---

## 软件测试

* 瀑布式开发模型
  * 软件测试三个阶段：
    * 单元测试
    * 集成测试
    * 系统测试
    * （压力测试）：验证系统的稳定性与承载能力
    * （用户测试）
* 基于敏捷式的开发

## JUnit 介绍

* java 单元测试”必备“工具
* 简单的单元测试工具
* 自动化测试工具

## JUnit 使用

注解：

* `@Test`
* `@Ignore`
* `@Test(expeted=Exception.class)` 功能要抛出异常
* `@Test(timeout = 1000)` 执行效率
* `@Before` 所有的测试场景都要先调用它
* `@After` 搜有测试之后要执行
* `@FixMethodOrder(MethodSorters.JVM)` 按照顺序依次执行


