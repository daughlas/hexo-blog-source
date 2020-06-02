---
title:  学习 MySQL 基础
date: 
categories:
- 数据库
tags:
- MySQL
---

# 05 | 检索数据：你还在使用 SELECT * 么？（二刷）

## SELECT 的执行顺序

1. 关键字的顺序是不能颠倒的：

```SQL
SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ...
```

2. SELECT 语句的执行顺序（在 MySQL 和 Oracle 中，SELECT 执行顺序基本相同）：

```SQL
FROM > WHERE > GROUP BY > HAVING > SELECT的字段 > DISTINCT > ORDER BY > LIMIT
```

关键字顺序和执行顺序是下面这样的

```SQL
SELECT DISTINCT player_id, player_name, count(*) as num #顺序5
FROM player JOIN team ON player.team_id = team.team_id #顺序1
WHERE height > 1.80 #顺序2
GROUP BY player.team_id #顺序3
HAVING num > 2 #顺序4
ORDER BY num DESC #顺序6
LIMIT 2 #顺序7
```

如果加上这个计算过程，完整的顺序是：
1、FROM子句组装数据
2、WHERE子句进行条件筛选
3、GROUP BY分组
4、使用聚集函数进行计算；
5、HAVING筛选分组；
6、计算所有的表达式；
7、SELECT 的字段；
8、ORDER BY排序
9、LIMIT筛选
所以中间有两个过程是需要计算的：聚集函数 和 表达式。其余是关键字的执行顺序，如文章所示。


在 SELECT 语句执行这些步骤的时候，每个步骤都会产生一个虚拟表，然后将这个虚拟表传入下一个步骤中作为输入。

## SQL 的执行原理


首先，你可以注意到，SELECT 是先执行 FROM 这一步的。在这个阶段，如果是多张表联查，还会经历下面的几个步骤：  

1. 首先先通过 CROSS JOIN 求笛卡尔积，相当于得到虚拟表 vt（virtual table）1-1；
2. 通过 ON 进行筛选，在虚拟表 vt1-1 的基础上进行筛选，得到虚拟表 vt1-2；
3. 添加外部行。如果我们使用的是左连接、右链接或者全连接，就会涉及到外部行，也就是在虚拟表 vt1-2 的基础上增加外部行，得到虚拟表 vt1-3。

当然如果我们操作的是两张以上的表，还会重复上面的步骤，直到所有表都被处理完为止。这个过程得到是我们的原始数据。

当我们拿到了查询数据表的原始数据，也就是最终的虚拟表 vt1，就可以在此基础上再进行 WHERE 阶段。在这个阶段中，会根据 vt1 表的结果进行筛选过滤，得到虚拟表 vt2。

然后进入第三步和第四步，也就是 GROUP 和 HAVING 阶段。在这个阶段中，实际上是在虚拟表 vt2 的基础上进行分组和分组过滤，得到中间的虚拟表 vt3 和 vt4。

当我们完成了条件筛选部分之后，就可以筛选表中提取的字段，也就是进入到 SELECT 和 DISTINCT 阶段。

首先在 SELECT 阶段会提取想要的字段，然后在 DISTINCT 阶段过滤掉重复的行，分别得到中间的虚拟表 vt5-1 和 vt5-2。

当我们提取了想要的字段数据之后，就可以按照指定的字段进行排序，也就是 ORDER BY 阶段，得到虚拟表 vt6。

最后在 vt6 的基础上，取出指定行的记录，也就是 LIMIT 阶段，得到最终的结果，对应的是虚拟表 vt7。

# 06 | 数据过滤：SQL 数据过滤有哪些方法？（二刷）

在 WHERE 子句中，你可以使用比较运算符、逻辑运算符和通配符这三种方式对检索条件进行过滤。

* 提升查询效率的一个很重要的方式，就是约束返回结果的数量
* 还有一个很有效的方式，就是指定筛选条件，进行过滤。

当 WHERE 子句中同时出现 AND 和 OR 操作符的时候，你需要考虑到执行的先后顺序，也就是两个操作符执行的优先级。一般来说 () 优先级最高，其次优先级是 AND，然后是 OR。

## 通配符：

（%）代表零个或多个字符，而（_）只代表一个字符。


建议你尽量少用通配符，因为它需要消耗数据库更长的时间来进行匹配。即使你对 LIKE 检索的字段进行了索引，索引的价值也可能会失效。如果要让索引生效，那么 LIKE 后面就不能以（%）开头，比如使用LIKE '%太%'或LIKE '%太'的时候就会对全表进行扫描。如果使用LIKE '太%'，同时检索的字段进行了索引的时候，则不会进行全表扫描。

保持高效率的一个很重要的原则，就是要避免全表扫描，所以我们会考虑在 WHERE 及 ORDER BY 涉及到的列上增加索引。



## 索引不生效

1. 不要在WHERE子句后面对字段做函数处理，同时也避免对索引字段进行数据类型转换
2. 避免在索引字段上使用<>，!=，以及对字段进行NULL判断（包括 IS NULL, IS NOT NULL）
3. 在索引字段后，慎用IN和NOT IN，如果是连续的数值，可以考虑用BETWEEN进行替换

因为在WHERE子句中，如果对索引字段进行了函数处理，或者使用了<>,!=或NULL判断等，都会造成索引失效。



# 07 | 为什么是 SQL 函数？为什么使用 SQL 函数可能会带来问题（二刷）

## 问题

1. 什么是 SQL 函数
2. 内置的 SQL 函数包括哪些？
3. 如何使用 SQL 函数对一个数据表进行操作，比如针对一个王者荣耀的英雄数据库，我们可以使用这些函数完成哪些操作？
4. 什么情况下使用 SQL 函数？为什么使用 SQL 函数有时候会带来问题？

## 什么是 SQL 函数

函数把我们经常使用的代码封装起来，需要的时候直接调用即可。这样既提高了代码效率，又提高了可维护性。

## 常用的 SQL 函数

1. 算术函数
2. 字符串函数
3. 日期函数
4. 转换函数

### 算术函数

| 函数名  | 定义                                                 |
| ------- | ---------------------------------------------------- |
| ABS()   | 取绝对值                                             |
| MOD()   | 取余                                                 |
| ROUND() | 四舍五入为指定小数位数，两个参数，字段名称，小数位数 |

SELECT ABS(-2)，运行结果为 2。

SELECT MOD(101,3)，运行结果 2。

SELECT ROUND(37.25,1)，运行结果 37.3。

### 字符串函数

| 函数名        | 定义                                                         |
| ------------- | ------------------------------------------------------------ |
| CONCAT()      | 将多个字符串拼接起来                                         |
| LENGTH()      | 计算字段长度，一个汉字算三个字符                             |
| CHAR_LENGTH() | 计算字符长度，汉字、数字、字母都算一个字符                   |
| LOWER()       | 英文转小写                                                   |
| UPPER()       | 英文转大写                                                   |
| REPLACE()     | 替换，参数：要替换的表达式或字段名，想要查找替换字符串、替换成哪个字符串 |
| SUBSTRING()   | 截取字符串，参数：要替换的表达式或字段名，开始截取的位置、想要街区的字符串长度 |

SELECT CONCAT('abc', 123)，运行结果为 abc123。

SELECT LENGTH('你好')，运行结果为 6。

SELECT CHAR_LENGTH('你好')，运行结果为 2。

SELECT LOWER('ABC')，运行结果为 abc。

SELECT UPPER('abc')，运行结果 ABC。

SELECT REPLACE('fabcd', 'abc', 123)，运行结果为 f123d。

SELECT SUBSTRING('fabcd', 1,3)，运行结果为 fab。

### 日期函数

| 函数名              | 定义                                | 例子                |
| ------------------- | ----------------------------------- | ------------------- |
| CURRENT_DATE()      | 系统当前时间                        | 2019-04-03          |
| CURRENT_TIME()      | 系统当前时间，没有具体的日期        | 09:48:52            |
| CURRENT_TIMESTAMP() | 系统当前时间，包括具体的日期 + 时间 | 2019-04-03 21:26:34 |
| EXTRACT()           | 抽取具体的年、月、日                |                     |
| DATE()              | 返回时间的日期部分                  | 2019-04-01          |
| YEAR()              | 返回时间的年份部分                  | 2019                |
| MONTH()             | 返回时间的月份部分                  | 04                  |
| DAY()               | 返回时间的天数部分                  | 01                  |
| HOUR()              | 返回时间的小时部分                  | 09                  |
| MINUTE()            | 返回时间的分钟部分                  | 48                  |
| SECOND()            | 返回时间的秒部分                    | 52                  |


这里需要注意的是，DATE 日期格式必须是 yyyy-mm-dd 的形式。如果要进行日期比较，就要使用 DATE 函数，不要直接使用日期与字符串进行比较，我会在后面的例子中讲具体的原因。

### 转换函数


| 函数名     | 定义               | 例子                                                       |
| ---------- | ------------------ | ---------------------------------------------------------- |
| CAST()     | 数据类型转换       | CAST(123.123 AS INT)，SELECT CAST(123.123 AS DECIMAL(8,2)) |
| COALESCE() | 返回第一个非空数值 | SELECT COALESCE(null,1,2)，运行结果为 1                    |


转换为小鼠的话，DECIMAL(a,b)来指定，其中 a 代表整数部分和小数部分加起来最大的位数，b 代表小数位数、


因为很多时候你无法确认 birthdate 的数据类型是字符串，还是 datetime 类型，如果你想对日期部分进行比较，那么使用DATE(birthdate)来进行比较是更安全的。

10：11

### SQL 函数有问题

DBMS 不同，实现的函数不同，使用 MySQL 的可移植性很差

????它会导致查询不走索引，直接全表遍历，导致慢查询-这才是最重要的问题。

## 规范

1. 关键字和函数名称全部大写
2. 数据库名、表名、字段名称全部小写
3. SQL 语句必须以分号结尾



# 08 | 什么事 SQL 的聚集函数，如何利用它们汇总表的数据？（二刷）

## 掌握

1. 聚集函数都有哪些，能否在一条 SELECT 语句中使用多个聚集函数；
2. 如何对数据进行分组，并进行聚类统计；
3. 如何使用 HAVING 过滤分组，HAVING 和 WHERE 的区别是什么。


## SQL 聚集函数

### 有哪些聚集函数

| 函数    | 说明   |
| ------- | ------ |
| COUNT() | 总行数 |
| MAX()   | 最大值 |
| MIN()   | 最小值 |
| SUM()   | 求和   |
| AVG()   | 平均值 |

COUNT(role_assist)会忽略值为 NULL 的数据行，而 COUNT(*) 只是统计数据行数，不管某个字段是否为 NULL。

### 使用

```SQL
SELECT COUNT(*), AVG(hp_max), MAX(hp_max), MIN(hp_max)  FROM heros WHERE role_main = '射手' OR role_assist = '射手';
```

```SQL
SELECT COUNT(DISTINCT hp_max)  FROM heros WHERE role_main = '射手' OR role_assist = '射手';
```

需要说明的是 AVG、MAX、MIN 等聚集函数会自动忽略值为 NULL 的数据行，MAX 和 MIN 函数也可以用于字符串类型数据的统计，如果是英文字母，则按照 A—Z 的顺序排列，越往后，数值越大。如果是汉字则按照全拼拼音进行排列。

### 分组聚集统计

使用一个字段进行分组

```SQL
SELECT COUNT(*), role_main FROM heros GROUP BY role_main;
```

使用多个字段进行分组

```SQL
SELECT COUNT(*) AS num, role_main, role_assist FROM heros GROUP BY role_main, role_assist ORDER BY num DESC;
```

### 过滤分组

过滤分组我们使用的是 HAVING。HAVING 的作用和 WHERE 一样，都是起到过滤的作用，只不过 WHERE 是用于数据行，而 HAVING 则作用于分组。

```SQL
SELECT COUNT(*) AS num, role_main, role_assist FROM heros GROUP BY role_main, role_assist HAVING num > 5 ORDER BY num DESC;
```

where 和 having

```SQL
SELECT COUNT(*) as num, role_main, role_assist
FROM heros
WHERE hp_max > 6000
GROUP BY role_main, role_assist
HAVING num > 5
ORDER BY num DESC
```

先使用 where 子句进行筛选，再使用 GROUP BY 进行分组，然后使用 HAVING 进行分组条件判断，然后使用 ORDER BY

记住顺序

```
SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ...
```



在执行顺序上，SELECT字段在GROUP BY和HAVING之后，不过在SELECT字段之前，已经计算了聚集函数，也就是COUNT(\*) as num。

聚集函数的计算在GROUP BY之后，HAVING之前

# 09 | 子查询：子查询的种类？如何提高子查询的性能（二刷ing）

SQL 还允许我们进行子查询，也就是嵌套在查询中的查询。

很多时候，我们无法直接从数据表中得到查询结果，需要从查询结果集中再次进行查询，才能得到想要的结果。这个“查询结果集”就是今天我们要讲的子查询。

## 问题

1. 子查询可以分为关联子查询和非关联子查询。我会举一个 NBA 数据库查询的例子，告诉你什么是关联子查询，什么是非关联子查询；
2. 子查询中有一些关键词，可以方便我们对子查询的结果进行比较。比如存在性检测子查询，也就是 EXISTS 子查询，以及集合比较子查询，其中集合比较子查询关键词有 IN、SOME、 ANY 和 ALL，这些关键词在子查询中的作用是什么；
3. 子查询也可以作为主查询的列，我们如何使用子查询作为计算字段出现在 SELECT 查询中呢？

## 什么是关联子查询，什么是非关联子查询

子查询从数据表中查询了数据结果，如果这个数据结果只执行一次，然后这个数据结果作为主查询的条件进行执行，那么这样的子查询叫做非关联子查询。

同样，如果子查询需要执行多次，即采用循环的方式，先从外部查询开始，每次都传入子查询进行查询，然后再将结果反馈给外部，这种嵌套的执行方式就称为关联子查询。

如果子查询的执行依赖于外部查询，通常情况下都是因为子查询中的表用到了外部的表，并进行了条件关联，因此每执行一次外部查询，子查询都要重新计算一次，这样的子查询就称之为关联子查询。比如我们想要查找每个球队中大于平均身高的球员有哪些，并显示他们的球员姓名、身高以及所在球队 ID。

在一个表里边进行的子查询


```SQL
SELECT player_name, height, team_id
FROM player AS a
WHERE height > (
	SELECT AVG(height)
	FROM player AS b
	WHERE a.team_id = b.team_id
);
```



## EXISTS 子查询

关联子查询通常也会和 EXISTS 一起来使用，EXISTS 子查询用来判断条件是否满足，满足的话为 True，不满足为 False。







# 12 ｜ 视图在 SQL 中的作用是什么，它是怎样工作的？

视图，也就是我们今天要讲的虚拟表，本身是不具有数据的，它是 SQL 中的一个重要概念