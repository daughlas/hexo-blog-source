---
title: javascript this
date: 
categories:
- 编程语言
tags:
- 编程语言
- javascript
- 待续
---
读《你不知道的 JS 》 第二章记录的笔记

# javascript this

this 函数可以自动引用合适的上下文对象很重要

```javascript
function identify() {
 return this.name.toUpperCase();
}

function speak() {
 var greeting = "Hello, I'm " + identify.call(this)
    console.log(greeting)
}

var me = {
 name: "Kyle"
}

var you = {
 name: "Reader"
}

identify.call(me)
identify.call(you)

speak.call(me)
speak.call(you)
```
不使用 this
```javascript
function identify(context) {
 return context.name.toUpperCase();
}

function speak(context) {
 var greeting = "Hello, I'm " + identify(context)
    console.log(greeting)
}

var me = {
 name: "Kyle"
}

var you = {
 name: "Reader"
}

identify.call(me)
identify.call(you)
```
## 误解

### 解释两个常见的对 this 的误解

#### 指向自身
解释一个误解，this 并不是指向函数本身
```javascript
function foo(num) {
    console.log('foo: ' + num) 
    console.log('this.count: '+this.count)
    console.log('foo.count: '+foo.count)
    this.count++ // 错误
    foo.count++ // 正确
}
var count = "global count"
foo.count = 0
var i
for(i = 0; i < 10; i++) {
    if (i > 5) {
        foo(i) // 错误
        foo.call(foo, i) // 正确
    }
}
console.log(foo.count)
```
一个具名的函数，在它内部可以使用函数名来引用自身。

#### 指向它的作用域
第二种常见的误解是，this 指向函数的作用域。
需要明确的是，this在任何情况下都不指向函数的词法作用域。在JavaScript内部，作用域确实和对象类似，可见的标识符都是它的属性。但是作用域“对象”无法通过JavaScript代码访问，它存在于JavaScript引擎内部。

```javascript
function foo() {
    var a = 'in foo'
    this.bar()
}

function bar() {
    console.log(this.a)
}

var  a = 'global'

foo()
```
不可能通过 this 来引用一个词法作用域内部的东西。每当你想要把this和词法作用域的查找混合使用时，一定要提醒自己，这是无法实现的。

### this 到底是什么
**this 是在运行时进行绑定的。并不是在编写时绑定的，他的上下文取决于函数调用时的各种条件。this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。**

当一个函数被调用的时候，会创建一个活动记录（有时候也称为执行上下文）。这个记录包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this 就是记录的其中一个属性，会在函数的执行过程中用到
学习this的第一步是明白this既不指向函数自身也不指向函数的词法作用域，你也许被这样的解释误导过，但其实它们都是错误的。this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。

## this 全面解析

### 调用位置
最重要的是要分析调用栈（就是为了到达当前执行位置所调用的所有函数）。我们关心的调用位置就在当前正在执行的函数的前一个调用中。

```javascript
function baz(){
    // 当前调用栈是：baz
    // 因此，当前调用位置是全局作用域
    console.log("baz");
    bar();    // <-- bar 的调用位置
}
function bar(){
    // 当前调用栈是baz-->bar
    // 因此，当前调用位置在baz中
    console.log("bar");
    foo();//<--foo的调用位置
}
function foo(){
    // 当前调用栈是 baz --> bar --> foo
    // 因此，当前调用位置在bar中
    debugger;
    console.log("foo");
}
baz();    // <-- baz的调用位置
```

### 绑定规则

#### 默认绑定

独立函数调用，this 指向全局对象。也是不满足其他条件时的默认规则。

```javascript
function foo() {
	console.log(this.a)
}
var a = 2
foo() // 2
```

#### 隐式绑定

另一条需要考虑的规则是调用位置是否有上下文对象，或者说是否被某个对象拥有或这包含。

```javascript
function foo () {
	console.log(this.a)
}
var a = 'global'
var obj = {
	a: 'in obj',
	foo: foo
}

obj.foo() // in obj
```

对象属性引用链中只有最顶层或者说最后一层会影响调用位置。

```javascript
function foo () {
	console.log(this.a)
}
var a = 'global'
var obj1 = {
	a: 'in obj1',
	foo: foo
}

var obj2 = {
	a: 'in obj2',
	obj1: obj1
}

obj2.obj1.foo() // in obj1
```



#### 隐式丢失

一个最常见的this绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把this绑定到全局对象或者undefined上，取决于是否是严格模式。

```javascript
function foo() {
	console.log(this.a)
}

var obj = {
	a: 'in obj',
	foo: foo
}

var bar = obj.foo // 函数别名
var a = "in global"
bar() // in global
```

参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值，所以结果和上一个例子一样。

如果把函数传入语言内置的函数而不是传入你自己声明的函数，会发生什么呢？结果是一样的，没有区别：

```javascript
function foo() {
	console.log(this.a)
}

var obj = {
	a: 'in obj',
	foo: foo
}

var bar = obj.foo // 函数别名
var a = "in global"
setTimeout(obj.foo, 100) // in global
```

JavaScript环境中内置的setTimeout()函数实现和下面的伪代码类似：

```javascript
function setTimeout(fn, delay) {
	// 等待 delay 毫秒
	fn(); // <-- 调用位置
}
```

就像我们看到的那样，回调函数丢失this绑定是非常常见的。除此之外，还有一种情况this的行为会出乎我们意料：调用回调函数的函数可能会修改this。在一些流行的JavaScript库中事件处理器常会把回调函数的this强制绑定到触发事件的DOM元素上。这在一些情况下可能很有用，但是有时它可能会让你感到非常郁闷。遗憾的是，这些工具通常无法选择是否启用这个行为。

实际上你无法控制回调函数的执行方式，因此就没有办法控制会影响绑定的调用位置。之后我们会介绍如何通过固定this来修复（这里是双关，“修复”和“固定”的英语单词都是fixing）这个问题。



#### 显式绑定

就像我们刚才看到的那样，在分析隐式绑定时，我们必须在一个对象内部包含一个指向函数的属性，并通过这个属性间接引用函数，从而把this间接（隐式）绑定到这个对象上。

那么如果我们不想在对象内部包含函数引用，而想在某个对象上强制调用函数，该怎么做呢？

```javascript
function foo() {
	console.log(this.a)
}

var obj = {
	a: 'in obj',
}
var a = "in global"
foo.call(obj)
foo()
```

##### 硬绑定

```javascript
function foo() {
	console.log(this.a)
}

var obj = {
	a: 'in obj',
	foo: foo
}
var a = "in global"
var baz = obj.foo
baz()

var bar = function() {
	foo.call(obj)
}
bar()
setTimeout(bar, 10)
bar.call(window)

```
