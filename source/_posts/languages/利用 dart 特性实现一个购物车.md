---
title: 利用 dart 特性实现一个购物车
date: 2019-07-20 09:25:33
categories: [ 编程语言 ]
tags: [ 编程语言, Dart]
---

> 摘要：简单介绍一下 dart 的一些特性，并利用这些特性，逐步优化一个购物车功能，使其逐步转化为 dart 风格的简介代码。

<!--more-->

不同于 java 的重载方法，Dart 认为重载会导致混乱，因此从设计之初就不支持重载，而是提供了可选命名参数和可选参数。

具体方式是，在声明函数是：

* 给参数增加{}，以 paramName: value 的方式指定调用参数，也就是可选命名参数；
* 给参数增加 []，则意味着这些参数是可以忽略的，也就是可选参数。

在使用这两种方式定义函数时，我们还可以在参数未传递时设置默认值。我以一个只有两个参数的简单函数为例，来和你说明这两种方式的具体用法：



```dart

//要达到可选命名参数的用法，那就在定义函数的时候给参数加上 {}
void enableFlags({bool bold, bool hidden}) => print("$bold , $hidden");

//定义可选命名参数时增加默认值
void enable1Flags({bool bold = true, bool hidden = false}) => print("$bold ,$hidden");

//可忽略的参数在函数定义时用[]符号指定
void enable3Flags(bool bold, [bool hidden]) => print("$bold ,$hidden");

//定义可忽略参数时增加默认值
void enable4Flags(bool bold, [bool hidden = false]) => print("$bold ,$hidden");

//可选命名参数函数调用
enable1Flags(bold: true, hidden: false); //true, false
enable1Flags(bold: true); //true, null
enable2Flags(bold: false); //false, false

//可忽略参数函数调用
enable3Flags(true, false); //true, false
enable3Flags(true,); //true, null
enable4Flags(true); //true, false
enable4Flags(true,true); // true, true
```

## dart 语言简化代码的一个例子

使得 Dart 能够写出更简介代码特性主要有以下三个：
* 命名构造函数
* 与 C++ 类似，Dart 支持初始化列表
* super(key: key)

下面我们会逐步改造一个例子，来看看 dart 究竟是如何简化开发的。例子很简单，实现的功能就是向购物车中添加商品，然后打印出购物车种的商品。

首先我们可以不利用 dart 的独特特性，用跟其他语言差不多的方式来实现这个功能，代码如下：

```dart
void main() {
  ShoppingCart sc = ShoppingCart('张三', '123456');
  sc.bookings = [Item('苹果', 10.0), Item('香蕉', 20.0)];
  print(sc.getInfo());
}

class Item {
  double price;
  String name;
  Item(name, price) {
    this.name = name;
    this.price = price;
  }
}

class ShoppingCart {
  String name;
  DateTime date;
  String code;
  List<Item> bookings;

  price() {
    double sum = 0.0;
    for (var i in bookings) {
      sum += i.price;
    }
    return sum;
  }

  ShoppingCart(name, code) {
    this.name = name;
    this.code = code;
    this.date = DateTime.now();
  }

  getInfo() {
    return "" +
      "购物车信息：" + 
      "\n---------------------"
      "\n用户名：" + name +
      "\n总价：" + price().toString() +
      "\n日期：" + date.toString() +
      "\n---------------------";
  }
}

```

下面，我们对这个例子的初始化过程进行优化，其他代码不变，利用 语法糖和初始化列表来简化初始化的过程，改变之后的部分代码如下：
```dart
// ...
class Item{
	double price;
	String name;
	Item(this.name, this.price);
}

class ShoppingCart {
	String name;
	DateTime date;
	String code;
	ShoppingCart(this.name, this.code) : date = DateTime.now();
	// ...
}
// ...
```

因为 ShoppingCart 和 Item 都有 name 和 price 属性，可以抽象成一个 Meta 类
```dart
// ...
class Meta {
	double price;
	String name;
	Meta(this.name, this.price);
}

class Item extends Meta {
	Item(name, price): super(name, price);
}

class ShoppingCart extends meta {
	DateTime date;
	String code;
	List<Item> bookings;
	
  // ...
	
	ShoppingCart(name, this.code) : date = DateTime.now(), super(name, 0);
	
  // ...
}
```

dart 语言还可让我们重载运算符，这样 ShoppingCart 中的 get price 方法就可以重写

```dart
// ...

class Item extends Meta {
	Item(name, price): super(name, price);
	Item operator + (Item item) => Item(name + item.name, price + item.price);
}

class ShoppingCart extends meta {
  // 重载了 + 之后，可以对 Item 的列表进行 reduce 操作了
	double get price => bookings.reduce((value, element) => value + element).price;
  // ...
}
```



最后修改一下 getInfo 方法，使用多行字符串简化字符串拼接，使用 ?? 运算符代替三元表达式，完整的例子代码就变成了下面这样。

```dart
class Meta {
	double price;
	String name;
	Meta(this.name, this.price);
}

class Item extends Meta {
	Item(name, price): super(name, price);
	Item operator + (Item item) => Item(name + item.name, price + item.price);
}

class ShoppingCart extends Meta {
	DateTime date;
	String code;
	List<Item> bookings;
	
	double get price => bookings.reduce((value, element) => value + element).price;
	
  ShoppingCart({name}) : this.withCode(name: name, code: null);
  ShoppingCart.withCode({name, this.code}) : date = DateTime.now(), super(name,0);
	
	getInfo() {
    return '''
    	购物车信息:
    	-----------------------------
    	用户名: $name
    	优惠码: ${code??"没有"}
    	总价: $price
    	Date: $date
    	-----------------------------
    ''';
  }
}

void main() {
  ShoppingCart sc = ShoppingCart.withCode(name:'张三', code: '123');
  sc.bookings = [Item('苹果', 10.0), Item('香蕉', 20.0)];
  print(sc.getInfo());
  
  ShoppingCart sc2 = ShoppingCart(name:'李四');
  sc2.bookings = [Item('火龙果', 100.0), Item('榴莲', 200.0)];
  print(sc2.getInfo());
}
```
> 参考资料：极客时间：Flutter核心技术与实战