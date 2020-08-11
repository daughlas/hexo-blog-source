---
title: ES5 的面向对象编程
date: 2017-01-03 18:16:13
categories: [编程语言]
tags: [编程语言, javascript]
front-matter: 我是一个测试的文章摘要
---

1. 用对象的思想去写代码，就是面向对象编程
2. 面向对象的特点： 
   1. 抽象： 抓住核心问题，把一样的东西放到一起
   2. 封装： 只能通过对象来访问方法
   3. 继承： 从已有对象上继承出新的对象，防止代码重写
   4. 多态： 一个接口根据进入的数据不同，进行不同的操作

3. 对象的组成
   1. 属性： 对象下面的变量———— 静态的，状态
   2. 方法（行为、操作） ———— 对象下面的函数： 过程的，动态的
4. ` var obj = {} ` 和 ` var obj = new Object() ` 是一摸一样的
5. 注意：
  1. 每一个实例也有一个constructor属性，默认调用prototype对象的constructor属性
  2. 如果替换了prototype对象，那么，下一步必然是为新的prototype对象加上constructor属性，并将这个属性指回原来的构造函数。

# 工厂方式 ———— 面向对象中封装函数



```
function createPerson(name) {
    var obj = new Object();
    obj.name = name;
    obj.showName= funtion() {
        alert(this.name)
    }

    return obj;
}

var p1 = create('小明');
p1.showName();
var p2 = create('小强');
p2.showName();

```

语法规定，当 new 去调用一个函数的时候，函数中的this就是创建出来的对象，而且函数的返回值直接就是this，这个叫做隐式返回。

new 后面调用的函数叫做构造函数

简单的写法如下： 

```
function CreatePerson(name) {
    this.name = name;
    this.showName= funtion() {
        alert(this.name)
    }
}

var p1 = new CreatePerson('小明');
p1.showName();
var p2 = new CreatePerson('小强');
p2.showName();

alert(p1.showName);
alert(p2.showName); // 这两行代码，返回的是同样的函数

alert(p1.shwoName == p2.showName);      // false，说明上面的一样只是看起来一样

```

## 对象的引用

```
var a = [1, 2, 3];
var b = [1, 2, 3];

alert(a == b);      // false;
```


基本类型的赋值的时候只是值的复制

```
var a = 5;
var b = a;
b += 3;
alert(b);       // 8
alert(a);       // 5
```

对象类型，赋值不仅是值的复制，而且也是引用的传递，a 和 b 指向的是同一个内存地址

```
var a = [1, 2, 3];
var b = a;
b.push(4);
alert(b);       // [1, 2, 3, 4]
alert(a);       // [1, 2, 3, 4]
```

只要在程序当中出现赋值，就要在内存中重新生成一个地址

```
var a = [1, 2, 3];
var b = a;
b = [1, 2, 3, 4];
alert(b);       // [1, 2, 3, 4]
alert(a);       // [1, 2, 3]
```

基本类型的比较，只要值相同就行

```
var a = 5;
var b = 5;
alert(a === b);     // true
```

脆响类型的比较，值和引用都要相同

```js
var a = [1, 2, 3];
var b = [1, 2, 3]];
alert(a === b);     // false
```

```js
var a = [1, 2, 3];
var b = a ;
alert(a === b);     // true
```

## 原型

作用： 去改写对象下面公用的方法或者属性，让公用的方法或者属性在内存中之存在一份，可以提高性能。
原型prototype，要写在构造函数的下面

```js
// 当作构造函数使用
var person = new Person("Stone", 28, "Software Engineer");
person.sayName(); // "Stone"

// 作为普通函数调用
Person("Sophie", 29, "English Teacher"); // 添加到 window
window.sayName(); // "Sophie"

// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Tommy", 3, "Baby");
o.sayName(); // "Tommy"
```



```js
function Person(){}

Person.prototype.name = "Stone";
Person.prototype.age = 28;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    console.log(this.name);
};

var person1 = new Person();
person1.sayName();   // "Stone"

var person2 = new Person();
person2.sayName();   // "Stone"

console.log(person1.sayName == person2.sayName);  // true
```

原型就像 CSS 中的 class， 普通方法就像是写在 CSS 中的 style

一般函数

```js
var arr = [1, 2, 4, 5];
var arr2 = [2, 4, 6, 8];

arr.sum = function() {
    var result = 0;
    for (var i = 0; i < this.length; i++) {
        result += this[i];
    }
    return result
}

arr2.sum = function() {
    var result = 0;
    for (var i = 0; i < this.length; i++) {
        result += this[i];
    }
    return result
}
alert(arr.sum);
alert(arr2.sum);

```

构造函数的写法

```js
var arr = [1, 2, 4, 5];
var arr2 = [2, 4, 6, 8];

Array.prototype.sum = function(){
    var result = 0;
    for (var i = 0; i < this.length; i++) {
        result += this[i];
    }
    return result
}

alert(arr.sum);
alert(arr2.sum);

```

原型的优先级不够高

```
var arr = [];
arr.number =10;
Array.prototype.number = 20;
alert(arr.number);          // 10
```

## 工厂方式之原型

```js
function CreatePerson(name) {
    this.name = name;
}

CreatePerson.prototype.showName = function() {
    alert(this.name);
}

var p1 = new CreatePerson('hello');
var p2 = new CreatePerson('world');

alert(p1.showName == p2.showName);          // 值相同，地址也相同

```

# 写法的总结（一阶段，混合写法）  

1. 不能公用的，变化的属性写在构造函数之中
2. 能够重用的，公用的方法写在原型里面

# 例子，面向对象选项卡

非面向对象的下发

```html
<div class="div1">
    <ipunt class="active" type="button">1</ipunt>
    <ipunt type="button">2</ipunt>
    <ipunt type="button">3</ipunt>

    <div style="display: block">1111111</div>
    <div>2222222</div>
    <div>333333333333</div>
</div>
```

```js
var oParent = document.getElementById('div1');
var aInput = oParent.getElementsByTagName('input');
var aDiv = oParent.getElementsByTagName('div');

for (var i=0; i<aInput.length; i++ ) {
    aInput[i].index = i;
    aInput[i].onclick = function() {
        for(var i =0; i<aInput.length; i++) {
            aInput[i].className='';
            aDiv[i].style.display = none;
        }
        this.className='active';
        aDiv[this.index].style.display = 'block'
    }
}

```

改写成面向对象的写法
现对函数进行变形：

1. 尽量不要出现函数的嵌套。
2. 可以有全局变量
3. 把 onload 中不是赋值的语句放到单独的函数中

例如上面的例子变形如下

```js
var oParent = nul;
var aInput = nul;
var aDiv = nul;

window.onload = funtion() {
    oParent = document.getElementById('div1');
    aInput = oParent.getElementsByTagName('input');
    aDiv = oParent.getElementsByTagName('div');

    init();
}


function init ()) {
    for (var i=0; i<aInput.length; i++ ) {
        aInput[i].index = i;
        aInput[i].onclick = change(i)
    }
}

function change() {
    for(var i =0; i<aInput.length; i++) {
        aInput[i].className='';
        aDiv[i].style.display = none;
    }
    this.className='active';
    aDiv[this.index].style.display = 'block'
}

```

在变型版的基础上改成面向对象

1. 全局变量就是属性
2. 函数就是方法
3. Onload 中创建对象
4. 改 this 指向问题：时间或者是定时器，尽量让面向对象中的 this 指向对象

```js
window.onload = function() {
    var t1 = new Tab();
    t1.init();
}

function Tab() {
    this.oParent = document.getElementsById('div1');
    this.aInput = this.oParent.getElementsByTagName('input');
    this.aDiv = this.oParent.getElementsByTagName('div');
}

Tab.prototype.init = function() {
    // 建一个This 来代指对象
    var _this = this;
    for (var i=0; i<this.aInput.length; i++) {
        this.aInput[i].index = i;
        this.aInput[i]onclick= function() {
           // this是按钮，传给change 
            _this.change(this);
        };
    }
}

// 里面有的this,有的指向对象，有的指向按钮

Tab.prototype.change = function(btn) {
    for (var i=0; i<this.aInput.length; i++) {
        this.aInput[i].className = '';
        this.aDiv[i].style.display = 'none';
    }
    btn.className = 'active';
    this.aDiv[btn.index].style.display = 'block';
}

```



如果有两个div想实现同样的功能，相对于封装函数，复用性更好，可以更好的配置

```js
window.onload = function() {
    var t1 = new Tab('div1');
    t1.init();

    var t2 = new Tab('div1');
    t2.init();
    t2.autoplay();
}

function Tab(id) {
    this.oParent = document.getElementsById(id);
    this.aInput = this.oParent.getElementsByTagName('input');
    this.aDiv = this.oParent.getElementsByTagName('div');
    this.iNow = 0;
}

Tab.prototype.init = function() {
    // 建一个This 来代指对象
    var _this = this;
    for (var i=0; i<this.aInput.length; i++) {
        this.aInput[i].index = i;
        this.aInput[i]onclick= function() {
           // this是按钮，传给change 
            _this.change(this);
        };
    }
}

// 里面有的this,有的指向对象，有的指向按钮

Tab.prototype.change = function(btn) {
    for (var i=0; i<this.aInput.length; i++) {
        this.aInput[i].className = '';
        this.aDiv[i].style.display = 'none';
    }
    btn.className = 'active';
    this.aDiv[btn.index].style.display = 'block';
}

Tab.prototype.autoPlay = funtion() {
    setInterval(function() {
        var _this. = this;

        if (_this.iNow<3) {
            _this.iNow++;
        } else {
            _this.iNow = 0;
            _this.iNow++;
        }
        

        for (var i=0; i<_this.aInput.length; i++) {
            _this.aInput[i].className = '';
            _this.aDiv[i].display: none;
        }
        _this.aInput[_this.iNow].className='active';
        _this.aDiv[_this.iNow].style.display ="block";
        
    }, 2000)
}
```





## 复习一下this 的指向问题

```js
oDiv.onclick = function() {
    this: oDiv
}



oDiv.onclick=show;
function show() {
    this: oDiv
}


oDiv.onclick = function() {
    show();
}
function show() {
    this: window
}
```





