---
title: 学习 Dart
date: 
categories:
- 混合开发
tags:
- 混合开发
- flutter
- Dart
---


# 学习 Dart 

Dart 认为重载会导致混乱，因此从设计之初就不支持重载，而是提供了可选命名参数和可选参数。



具体方式是，在声明函数时：

* 给参数增加{}，以 paramName: value 的方式指定调用参数，也就是可选命名参数；
* 给参数增加 []，则意味着这些参数是可以忽略的，也就是可选参数。



在使用这两种方式定义函数时，我们还可以在参数未传递时设置默认值。我以一个只有两个参数的简单函数为例，来和你说明这两种方式的具体用法：



```dart

//要达到可选命名参数的用法，那就在定义函数的时候给参数加上 {}
void enable1Flags({bool bold, bool hidden}) => print("$bold , $hidden");

//定义可选命名参数时增加默认值
void enable2Flags({bool bold = true, bool hidden = false}) => print("$bold ,$hidden");

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

## ?

* 命名构造函数
* 与 C++ 类似，Dart 支持初始化列表
* super(key: key)

## 土鳖的例子

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
      "\n优惠码：" + code +
      "\n总价：" + price().toString() +
      "\n日期：" + date.toString() +
      "\n---------------------";
  }
}

```



## 高级的例子1（修改构造方法）

```dart
void main() {
  ShoppingCart sc = ShoppingCart('张三', '123456');
  sc.bookings = [Item('苹果', 10.0), Item('香蕉', 20.0)];

  print(sc.getInfo());
}

class Item{
	double price;
	String name;
	Item(this.name, this.price);
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
	ShoppingCart(this.name, this.code) : date = DateTime.now();
	
	getInfo() {
    return "" +
      "购物车信息：" + 
      "\n---------------------"
      "\n用户名：" + name +
      "\n优惠码：" + code +
      "\n总价：" + price().toString() +
      "\n日期：" + date.toString() +
      "\n---------------------";
  }
}
```



## 高级的例子2（类抽象）

```dart
void main() {
  ShoppingCart sc = ShoppingCart('张三', '123456');
  sc.bookings = [Item('苹果', 10.0), Item('香蕉', 20.0)];

  print(sc.getInfo());
}

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
	
	double get price {
		double sum = 0.0;
    for (var i in bookings) {
      sum += i.price;
    }
    return sum;
	}
	
	ShoppingCart(name, this.code) : date = DateTime.now(),super(name, 0);
	
	getInfo() {
    return "" +
      "购物车信息：" + 
      "\n---------------------"
      "\n用户名：" + name +
      "\n优惠码：" + code +
      "\n总价：" + price().toString() +
      "\n日期：" + date.toString() +
      "\n---------------------";
  }
}
```



## 高级的例子3（方法改造）

```dart
void main() {
  ShoppingCart sc = ShoppingCart('张三', '123456');
  sc.bookings = [Item('苹果', 10.0), Item('香蕉', 20.0)];

  print(sc.getInfo());
}

class Meta {
	double price;
	String name;
	Meta(this.name, this.price);
}

class Item extends Meta {
	Item(name, price): super(name, price);
	Item operator + (Item item) => Item(name + item.name, price + item.price);
}

class ShoppingCart extends meta {
	DateTime date;
	String code;
	List<Item> bookings;
	double get price => bookings.reduce((value, element) => value + element).price;
	
	ShoppingCart(this.name, this.code) : date = DateTime.now();
	
	getInfo() {
    return '''
    	购物车信息:
    	-----------------------------
    	用户名: $name
    	优惠码: $code
    	总价: $price
    	Date: $date
    	-----------------------------
    '''
  }
}
```



##高级的例子4（对象初始化方式的优化）

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





