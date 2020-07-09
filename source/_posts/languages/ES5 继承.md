---
title: ES5 继承
date: 
categories:
- 编程语言
tags:
- 编程语言
- javascript
---

```javascript
function Animal() {
    this.species = "动物";
}

function Cat(name, color) {
    this.name = name;
    this.color = color;
}
```

想要使猫继承动物，下面介绍物种方法

## 一、构造函数绑定

使用 call 和 apply 方法，将父对象的构造函数绑定在子对象上

```javascript

function Cat(name, color) {
    Animal.apply(this, arguments);
    this.name = name;
    this.color = color;
}
var cat1 = new Cat("大毛","黄色");
alert(cat1.species); // 动物

```
还有下面的例子
```javascript
function Super(value) {
    this.val = value;
    this.arr = [1];
    this.fun = function() {
        
    }
}
function Sub(val) {
    Super.call(this, val)
}

var sub1 = new Sub(1);
var sub2 = new Sub(2);

sub1.arr.push(2);
console.log(sub1);
console.log(sub2);
console.log(sub1.fun === sub2.fun);		// false
```

**核心：**

借父类的构造函数来增强子类的实例，等于是把父类的实例属性复制了一份给子类实例装上了（完全没有用到原型）

**优点：**

1. 解决了子类实例共享父类引用类型属性的问题
2. 创建实例时，可以向父类构造函数传递参数。

**缺点：**

1. 无法实现函数的复用，同一个函数，每个子类实例都会创建各自与之对应的方法。太多了会影响性能，内存爆炸。

## 二、prototype 模式

```javascript

Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
var cat1 = new Cat("大毛","yellow");
alert(cat1.species); // 动物

```

代码的第一行完全删除了 prototype 原来的值，赋予了一个新的值
代码的第二行 `Cat.prototype.constructor = Cat`

任何一个prototype对象都有一个constructor属性，指向它的构造函数。如果没有"Cat.prototype = new Animal();"这一行，Cat.prototype.constructor是指向Cat的；加了这一行以后，Cat.prototype.constructor指向Animal。

更重要的是，每一个实例也有一个constructor属性，默认调用prototype对象的constructor属性，`alert(cat1.constructor == Cat.prototype.constructor); // true`。

这是很重要的一点，编程时务必要遵守。下文都遵循这一点，即如果替换了prototype对象，那么，下一步必然是为新的prototype对象加上constructor属性，并将这个属性指回原来的构造函数。

## 三、直接继承prototype

由于Animal对象中，不变的属性都可以直接写入Animal.prototype。所以，我们也可以让Cat()跳过 Animal()，直接继承Animal.prototype。

```javascript

function Animal(){ }
Animal.prototype.species = "动物";
Cat.prototype = Animal.prototype;
Cat.prototype.constructor = Cat;
var cat1 = new Cat("大毛","黄色");
alert(cat1.species); // 动物

```

与前一种方法相比，这样做的优点是效率比较高（不用执行和建立Animal的实例了），比较省内存。缺点是 Cat.prototype和Animal.prototype现在指向了同一个对象，那么任何对Cat.prototype的修改，都会反映到Animal.prototype。所以上面的代码会把animal.prototype.constructor 的属性也跟着改成 Cat。

## 四、利用空对象作为中介

```javascript
var F = function() {}
F.prototype = Animal.prototype;
Cat.prototype = new　F();
Cat.prototype.constructor = Cat;

```

封装一下继承的函数

```javascript

function extend(Child, Parent) {
    var F = function() {};
    F.prototype = Parent.prototype;
    Child.prototype = new F();
    Child.prototype.constructor = Child;
    Child.uber = Parent.prototype;
}
```

最后一行为子对象设一个uber属性，这个属性直接指向父对象的prototype属性。（uber是一个德语词，意思是"向上"、"上一层"。）这等于在子对象上打开一条通道，可以直接调用父对象的方法。这一行放在这里，只是为了实现继承的完备性，纯属备用性质。

## 五、拷贝继承

```javascript

function Animal() {};
Animal.prototype.species = "动物";

function extend2(Child, Parent) {
    var p = Parent.prototype;
    var c = Child.prototype;
    for (var i in p) {
        c[i] = p[i]
    }
    c.uber = p;
}
```

## 六、组合继承（最常用）

为了解决借用构造函数方式的问题（无法实现函数复用），有了下面的办法。

```javascript
function Super(val) {
    this.val = val;
    this.arr = [1];
}
Super.prototype.fun1 = function() {}
// 继承父函数的基本属性和引用属性
function Sub(val) {
    Super.call(this, val)
}
// 继承父函数，实现函数的复用，这句是不是有没有都行啊
Sub.prototype = new Super();

var sub1 = new Sub(1);
var sub2 = new Sub(2);

sub1.arr.push(2);
console.log(sub1);
console.log(sub2);
console.log(sub1.fun === sub2.fun);		// true
```

**核心：**

把实例函数都放在原型对象上，以实现函数复用。同时还要保留借用构造函数方式的优点。

**优点：**

1. 不存在引用属性共享问题
2. 可传参
3. 函数可复用

**缺点：**

1. 子函数的原型上有一套多余的父函数的属性



## 七、寄生组合继承（最佳方式）

```javascript
function beget(obj){   // 生孩子函数 beget：龙beget龙，凤beget凤。
    var F = function(){};
    F.prototype = obj;
    return new F();
}
function Super(){
    // 只在此处声明基本属性和引用属性
    this.val = 1;
    this.arr = [1];
}
//  在此处声明函数
Super.prototype.fun1 = function(){};
Super.prototype.fun2 = function(){};
//Super.prototype.fun3...
function Sub(){
    Super.call(this);   // 核心
    // ...
}
var proto = beget(Super.prototype); // 核心
proto.constructor = Sub;            // 核心
Sub.prototype = proto;              // 核心
 
var sub = new Sub();
alert(sub.val);
alert(sub.arr);
```