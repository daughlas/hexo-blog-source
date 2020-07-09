---
title: ES5 面向对象演变
date: 
categories:
- 编程语言
tags:
- 编程语言
- javascript
---

## 一、生成实例对象的原始模式

```javascript
var Cat = {
    name: "",
    color: ""
}

var cat1 = {};
cat1.name = "大毛";
cat1.color = "yello";

var cat2 = {};
cat2.name = "二毛";
cat2.color = "black;l
```

这种写法的问题是：
1. 如果定义很多实例写起来很麻烦
2. 实例与原型之间没有什么联系

## 二、原始模式的改进

```javascript

function Cat(name, color) {
    return {
        name: name,
        color: color
    }
}

var cat1 = Cat("大毛","yellow");
var cat2 = Cat("二毛", "black");

```

问题：不能反映 cat1 和 cat2 有没有内在的联系，不能反映出它们是同一个原型对象的实例。

## 三、构造函数模式

为了解决从原型对象生成实例的问题，Javascript 提供了一个构造函数（Vonstructor）模式。

所谓构造函数，其实就是普通函数，内部使用了 this 变量，对构造函数使用 new 运算符，就能生成实例，并且 this 变量会绑定在实例对象上。

```javascript
function Cat(name, color) {
    this.name = name;
    this.color = color;
}

var cat1 = new Cat("大毛","yellow");
var cat2 = new Cat("二毛", "black");

alert(cat1.constructor == Cat); // true
alert(cat2.constructor == Cat); // true

alert(cat1 instanceof Cat); // true
alert(cat2 instanceof Cat); // true

```

### 构造函数模式的问题

浪费内存，请看下面的这段代码

```javascript

unction Cat(name, color) {
    this.name = name;
    this.color = color;
    this.type = "猫"；
    this.eat = function() {
        return "开饭啦";
    }
}

var cat1 = new Cat("大毛","yellow");
var cat2 = new Cat("二毛", "black");

alert(cat1.type); // 猫
alert(cat1.eat); // 开饭啦
alert(cat2.eat); // 开饭啦

alert(cat1.eat == cat2.eat); // false

```

从上面可以看出，对于每一个实例对象，type 和 eat 的内容是一样的，但是，没生成一个实例，都必须为重复的内容，多占一些内存。


## 五、Prototype 模式

为了让eat 和 type 这些公共的属性和方法只生成一次，然后所有实例都指向那个内存地址，就有了 Prototype 这个神奇的模式。

Javascript规定，每一个构造函数都有一个prototype属性，指向另一个对象。**这个对象的所有属性和方法，都会被构造函数的实例继承**。

我们应该把那些不变的属性和方法，直接定义在 prototype 对象上。

```javascript
function Cat(name,color){
　　this.name = name;
　　this.color = color;
}

Cat.prototype.type = "猫";
Cat.prototype.eat = function() {
    alert("吃老鼠")
};

var cat1 = new Cat("大毛","yellow");
var cat2 = new Cat("二毛", "black");

alert(cat1.type); // 猫
alert(cat1.eat); // 开饭啦
alert(cat2.eat); // 开饭啦

alert(cat1.eat == cat2.eat); // true

alert(Cat.prototype.isPrototypeOf(cat1)); // true
alert(Cat.prototype.isPrototypeOf(cat2)); // true

alert(cat1.hasOwnProperty("name")); // true
alert(cat1.hasOwnProperty("type")); //false

alert("name" in cat1) // true
alert("type" in cat1) // true

```

每个实例对象都有一个hasOwnProperty()方法，用来判断某一个属性到底是本地属性，还是继承自prototype对象的属性。
in运算符可以用来判断，某个实例是否含有某个属性，不管是不是本地属性。


