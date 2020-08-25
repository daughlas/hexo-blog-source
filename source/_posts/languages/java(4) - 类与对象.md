---
title: java(4) - 类与对象
date: 2020-04-03 11:23:30
categories:
- 编程语言
tags:
- 编程语言
- java
---

> 摘要：这是 《JAVA 核心技术》 第 4 章 类与对象

<!--more-->


# 第 4 章 类与对象

## 4.1 面向对象程序设计概述

* 结构化程序设计，算法第一，为了解决一个问题，先设计一系列的算法，然后在考虑数据的存储方式。
* 面向对象程序设计，将数据放在第一位，然后再去考虑操作数据的算法

### 4.1.3 识别类

如何下手：首先从设计类开始，然后再往每个类中添加方法。

**找名词与动词**：首先分析问题中遇到的名词，再将动词作为方法传给他

> 例如，订单系统中
>
> 名词有：
>
> * 商品（Item）
> * 订单（Order）
> * 送货地址（Shipping address）
> * 付款（Payment）
> * 账户（Account）
>
> 动词有：
>
> * 添加（商品被添加到订单中）
> * 发送、取消（订单被发送、取消）
> * 付款、支付（订单被付款）

### 4.14 类与类之间的关系

* 依赖（uses-a）：
  * 如果一个类的方法操纵另一个类的对象，我们就说一个类依赖于另一个类。
  * 应该尽可能将相互依赖的类减至最少，让类之间的耦合度最小
* 聚合（has-a)：
  * 类A 的对象包含类 B 的对象，例如：一个 Order 对象包含一些 Item 对象
* 继承（is-a)：

## 4.2 使用预定义类

适用对象，首先要指定其初始状态并构造对象。

JAVA 使用构造器（constructor)构造新实例，constructor 用来构造并初始化对象，构造器的名字应该与类名相同，因此 Date 类的构造器名为 Date

> 一定要认识到：一个对象变量并没有实际包含一个对象，而仅仅引用一个对象。new 操作符返回的值是一个引用

GMT 格林威治时间

UTC 从 1970年1月1日00：00：00 到现在的毫秒数

* localDate 的静态工厂方法
  * of(199, 12, 31)
  * getYear()
  * getMonthValue()
  * getDayofMonth()
  * plusDays()

### 更改器方法和访问器方法

* 访问其方法：accessor method，只访问对象而不修改对象的方法

## 4.3 用户自定义类

文件名必须与 public 类的名字相匹配。在一个，源文件中只能有一个公有类，但可以有任意数目的非公有类。

将类的属性设置为 public（如public String name） 或者对属性添加域访问器（如 getName）,都可以对外暴露name 属性，但是用域访问器，也就是封装起来的好处是：当仅用 get 的时候，可以保证属性不被意外的修改；当 set 某个域的时候，前者可以在程序的所有地方设置，修改的时候处处都要修改，而后者只要修改 setName 方法就可以了。

在有些时候，需要获得或设置实例域的值。因此，应该提供下面三项内容：

* 私有的数据域
* 公有的域访问器方法
* 一个公有的域更改器方法

好处：

* 可以更改内部实现，不会影响程序其他部分。
  * `getName(){return name}` 换成`getName(){return this.firstName + this.lastName}`
* 更改器方法可以执行错误检查，直接对域值进行赋值将不会进行这些处理
* 公有域非常危险，所以应该将所有的数据域设置为私有域。

>  注意：**不要编写返回引用可变对象的访问器方法。因为外部获取一个属性之后，对该属性调用更改器方法，会跳过类的更改器而直接修改类的属性。**

如何编写可变对象的访问器

```java
class Employee {
	// ...
	public Date getHireDay() {
		return (Date) hireDay.clone();
	}
	// ...
}
```

### 隐式参数与显式参数

出现在方法名前的 类对象 是隐式参数，在每一个方法中，关键词 this 表示隐式参数

位于方法名后边括号之中的参数为显示参数

### 4.3.7 基于类的访问权限

从前面已经知道，方法可以访问所调用对象的私有数据。一个方法可以访问所属类的所有对象的私有数据，这令很多人感到奇怪！

```java
class Employee {
	// ...
	public boolean equals(Employee other) {
		return return name.equals(other.name)
	}
	// ...
}
```

调用 `if (harry.equals(boss))` ，这个方法不仅访问了 harry 的私有域，还访问了 boss 的私有域。这是因为 boss 是 Employee 类的对象，而Employee 类的方法可以访问 Employee 类的任何一个对象的私有域。

### 4.3.8 私有方法

只要方法是私有的，更改程序的时候，就可以去确信，他不会被程序外部的其他数据操作调用，只要保证类的内部代码正常运作，删除这个私有方法不会影响程序的其他部分。

### 4.3.9 final 实例域

* 构建对象时，必须初始化这样的域
* 一个构造器执行之后，这个域的值被设置，并且在后面的操作中，不能再对它进行修改。

## 4.4 静态域和静态方法

### 4.4.1 静态域

```java
class Employee {
  // ....
	private static int nextId = 1;
	private int id;
  // ....
  public void setId() {
    id = nextId;
    nexId++
  }
}
```

* 每一个雇员对象都有一个自己的 id 域
* 这个类的所有实例将共享一个 nextId域
* 即使没有一个雇员对象，静态域 nextId 也存在。它属于类，二不属于任何独立的对象。
* 静态域被称为类域

### 4.4.2 静态常量

静态变量使用得比较少，但是静态常量却使用得比较多

```java
public class Math {
	// ...
	public static final double PI = 3.1415926
	// ...
}
```



```java
public class System {
	// ...
	public static final PrintScream out = ...;
	// ...
}
```



这样可以使用Math.PI，就不用通过类的对象访问 PI 了，而且不用每个实例保存一个PI 的拷贝

> 辨别：每个类都可以修改公有域（public 属性），不要将域（属性）设计成public。
>
> 公有常量（final 域）可以设置成 public，因为设置成 final 不允许修改。

### 4.4.3 静态方法

静态方法是一种不能向对象实施操作的方法，也就是没有隐式参数、this 参数的方法。例如 Math.pow(x, a)。

静态方法不能访问实例域（类属性），但是可以访问自身类中的静态域。

```java
class Employee {
  // ....
	private static int nextId = 1;
  // ....
  public static int getNextId() {
    return nextId;
  }
}
```

可以通过 类名调用这个方法 `int n = Employee.getNextId();`

静态方法和类的实例对象没有关系，建议用类名调用静态方法。

在下面两种情况使用静态方法：

* 一个方法不需要访问对象状态，其所需要的参数都是通过显示参数提供的（Math.pow()）
* 一个方法只需要访问类的静态域（Employee.getNextId）

### 4.4.4 工厂方法

LocalDate.now 和 LocalDate.of 就是工厂方法，他们是 LocalDate 的类静态工厂方法

```java
NumberFormat currencyFormatter = NumberFormat.getCurrencyInstance();
NumberFormat percentFormatter = NumberFormat.getPercentInstance();
double x = 0.1;
System.out.println(currencyFormatter.format(x));
System.out.println(percentFormatter.format(x));
```

不明觉厉：为什么 NumberFormat 类不利用构造器完成这些操作的原因：

* 无法命名构造器。构造器的名字必须与类名相同。但是，这里希望将的道德货币实例和百分比实例采用不同的名字。
* 当使用构造器时，无法改变所构造的对象的类型。而 Factory 方法将返回一个 DecimalFormat 类对象，这是NumberFormat 的子类。

### 4.4.5 main 方法