---
title: javascript 作用域
date: 
categories:
- 编程语言
tags:
- 编程语言
- java
---

# javascript 函数的执行

闭包相关知识图谱

* 闭包
  * 表达式部分：lamada 表达式
  * 环境部分
    * 环境
      * 变量环境
      * 词法环境
      * this值
    * 标识符列表：函数中用到的未声明的变量

javascript 函数相关知识图谱：

* javascript 函数
  * 表达式部分：函数
  * 环境部分：函数的词法环境部分组成
    * 函数里面不带 var 、let、const 的变量
    * 词法作用域（执行上下文的一部分）
      * scope
      * this值

从图上可以看出，二者是可以对应起来的。

## 闭包

在上世纪60年代，主流编程语言是基于 lamada 演算的函数式编程语言。

对闭包，一个不太精确的描述是，**带有一系列信息的 lamada 表达式**（在函数式语言中，lamada表达式就是函数）。

进一步理解，**闭包是一个绑定了执行环境的函数**

闭包不同于普通函数之处在于，他**携带了执行的环境**。

> 误区：执行上下文或者作用域（scope，ES3中规定的执行上下文的一部分）就是闭包（这是错的）

**javascript 中闭包对应的概念就是函数。**

## 执行上下文：执行的基础设施

JavaScript 标准把一段代码（包括函数），执行所需的所有信息定义为：“**执行上下文**”。

### 执行上下文的演变

#### ES3中的执行上下文

* scope：作用域，也叫作用域链
* variable object： 变量对象，用于存储变量的对象
* this value： this 值

#### ES5 中的执行上下文

* lexical enrironment：词法环境，当获取变量时使用。
* variable environment：变量环境，当声明变量时使用。
* this value：this 值

#### ES 2018中的执行上下文

* lexical enrironment：词法环境，当获取变量时使用或者 this 值时使用。
* variable environment：变量环境，当声明变量时使用。
* code evaluation state：用于恢复代码执行位置。
* Function：执行的任务是函数时使用，表示正在执行的函数
* ScriptOrModule：执行的任务是脚本或模块时使用，表示正在执行的代码
* Realm：使用的基础库和内置对象实例。
* Generator：仅生成器上下文有这个属性，表示当前生成器

this 被归入了 lexical environment



### var 声明和赋值

var 会穿透 for、if 等语句。

因此也就有了立即执行函数IIFE，创建一个立即执行的函数来构造一个新的作用域，控制 var 的范围。



### let

以下语句会产生 let 使用的作用域

* for
* if
* switch
* try / catch / finally

### Realm

领域、范围



# 函数的种类

一旦上下文被切换，整个语句的效果可能都会发生改变。那么，切换上下文的时机就显得非常重要了。

在 JavaScript，切换上下文最主要的场景是函数调用。在这一课，我们就来讲讲函数调用切换上下文的事情。我们在讲函数调用之前，首先来认识一下函数家族。



## 函数的分类

### 普通函数

用 function 关键字定义的函数

```js
function foo() {
	// code
}
```

### 箭头函数

用 => 运算符定义的函数

```js
const foo = () => {
	// code
}
```

### 方法

在 class 中定义的函数

```js
class C {
	foo() {
		// code
	}
}
```

### 生成器函数

用 function* 定义的函数

```js
function* foo() {
	// code
}
```

### 类

用 class 定义的类，实际上也是函数

```js
class Foo {
	constructor() {
		// code
	}
}
```

### 异步函数

普通函数、箭头函数、和生成器函数加上 async 关键字

```
async function foo() {
	// code
}

const foo = async () => {
	// code
}
async function foo*() {
	// code
}
```



## this 关键字的行为

对普通变量而言，这些函数并没有本质区别，都是遵循了“继承定义时环境”的规则，它们的一个行为差异在于 this 关键字。

this 是执行上下文中很重要的一个组成部分。同一个函数调用方式不同，得到的 this 值也不同。

#### 普通函数的 this

```js
function showThis() {
	console.log(this)
}

var o = {
	showThis: showThis
}

showThis()	// global
o.showThis()	// o
```

普通函数的 this 值由“调用它所使用的引用”决定，其中奥秘就在于：我们获取函数的表达式，它实际上返回的并非函数本身，而是一个 Reference 类型（记得我们在类型一章讲过七种标准类型吗，正是其中之一）。

Reference 类型由两部分组成：一个对象和一个属性值。不难理解 o.showThis 产生的 Reference 类型，即由对象 o 和属性“showThis”构成。

当做一些算术运算（或者其他运算时），Reference 类型会被解引用，即获取真正的值（被引用的内容）来参与运算，而类似函数调用、delete 等操作，都需要用到 Reference 类型中的对象。

在这个例子中，Reference 类型中的对象被当作 this 值，传入了执行函数时的上下文当中。

至此，我们对 this 的解释已经非常清晰了：调用函数时使用的引用，决定了函数执行时刻的 this 值



#### 箭头函数的 this


```
const showThis = () => {
	console.log(this)
}

var o = {
	showThis: showThis
}

showThis()	// global
o.showThis()	// global
```

我们看到，改为箭头函数后，不论用什么引用来调用它，都不影响它的 this 值。

#### 方法的 this

```
class C {
	showThis() {
		console.log(this)
	}
}

var o = new C()
var showThis = o.showThis()

showThis() // undefined
o.showThis() // o
```



### 其他函数

* 生成器函数、异步生成器函数和异步普通函数跟普通函数行为是一致的
* 异步箭头函数与箭头函数行为是一致的



### this 关键字的机制

在 JavaScript 标准中，为函数规定了用来保存定义时上下文的私有属性[[Environment]]。

当一个函数执行时，会创建一条新的执行环境记录，记录的外层词法环境（outer lexical environment）会被设置成函数的[[Environment]]。

这个动作就是切换上下文了。

JavaScript 用一个栈来管理执行上下文，这个栈中的每一项又包含一个链表。

当函数调用时，会入栈一个新的执行上下文，函数调用结束时，执行上下文被出栈。

而 this 则是一个更为复杂的机制，JavaScript 标准定义了 [[thisMode]] 私有属性。

[[thisMode]] 私有属性有三个取值：

* lexical：表示从上下文中找 this，这对应了箭头函数。
* global：表示当 this 为 undefined 时，取全局对象，对应了普通函数。
* strict：当严格模式时使用，this 严格按照调用时传入的值，可能为 null 或者 undefined。

非常有意思的是，方法的行为跟普通函数有差异，恰恰是因为 class 设计成了默认按 strict 模式执行。

我们可以用 strict 达成与上一节中方法的例子一样的效果:

```js
"use strict"
function showThis(){ 
	console.log(this);
}
var o = {
	showThis: showThis
}

showThis(); // undefined
o.showThis(); // o
```

函数创建新的执行上下文中的词法环境记录时，会根据[[thisMode]]来标记新纪录的[[ThisBindingStatus]]私有属性。

代码执行遇到 this 时，会逐层检查当前词法环境记录中的[[ThisBindingStatus]]，当找到有 this 的环境记录时获取 this 的值。

这样的规则的实际效果是，嵌套的箭头函数中的代码都指向外层 this，例如：

```
var o = {a: 1, b: 2, c: 3}
o.foo = function() {
	console.log(this)
	return () => {
		console.log(this)
		return () => console.log(this)
	}
}
o.foo()()();//o, o, o
```



### 操作 this 的内置函数

```
function foo(a, b, c) {
	console.log(this)
	console.log(a, b, c)
}

foo.call({d:4}, 1, 2, 3)
foo.apply({e: 5}, [1, 2, 3])
```



# javascript 作用域



# javascript 闭包

