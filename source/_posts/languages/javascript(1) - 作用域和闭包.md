---
title: javascript(1) - 作用域和闭包
date: 
categories: [编程语言]
tags: [编程语言, javascript, 待续, 摘录]
---
> 记录《你不知道的 Javascript》上册，第一部分，作用域与闭包部分的读书笔记，主要是摘录，还未做整理。
读《你不知道的 JS 》

## 两个例子
### 闭包的例子
```javascript
function foo() {
	var a = 10
	return function() {
		
        return ++a
	}
}

function bar() {
	var a = 10
    return ++a
}

var g = foo()
console.log(g()); // 11
console.log(g()); // 12
console.log(g()); // 13
console.log('------------------')
console.log(foo()()); // 11
console.log(foo()()); // 11
console.log(foo()()); // 11
console.log('------------------')
console.log(bar()); // 11
console.log(bar()); // 11
console.log(bar()); // 11
```

### 作用域
下面两种函数 foo 和 bar 实际上是等价的。原理是 var 的声明提前，提前到当前作用域开始的地方
```javascript
function foo(isTrue) {
	if (isTrue) {
		var a = 10
	}
	return a
}

function bar(isTrue) {
	var a
	if (isTrue) {
		a = 10
	}
	return a
}

console.log(foo(true)) // 10
console.log(foo(false)) // undefined
console.log(bar(true)) // 10
console.log(bar(false)) // undefined
```

```javascript
for(var i = 0; i < 10; i++) {
	setTimeout(function() {
		console.log(i)
	}, 100 * i)
}

for(var i = 0; i < 10; i++) {
	(function(j) {
		setTimeout(function() {
			console.log(j)
		}, 100 * j)
	})(i)
}
```

```javascript
for(var i = 0; i < 10; i++) {
	setTimeout(function() {
		console.log(i)
	}, 100 * i)
}

for(var i = 0; i < 10; i++) {
	(function(j) {
		setTimeout(function() {
			console.log(j)
		}, 100 * j)
	})(i)
}
```

## 1 作用域是什么

为了解决程序需要时如何找到各种变量，工程师需要一套设计良好的规则来存储变量，并且之后可以方便地找到这些变量。 这套规则被称为作用域。

尽管通常将 JavaScript 归类为“动态”或“解释执行”语言，但事实上它是一门编译语言。 

###编译

在传统编译语言的流程中，程序中的一段源代码在执行之前会经历三个步骤，统称为“编 译”。 

对于 JavaScript 来说，大部分情况下编译发生在代码执行前的几微秒(甚至更短!)的时 间内。 JavaScript 引擎不会有大量的(像其他语言编译器那么多的)时间用来进行优化，因 为与其他语言不同，JavaScript 的编译过程不是发生在构建之前的。 

任何 JavaScript 代码片段在执行前都要进行编译(通常就在执行前)。因此， JavaScript 编译器首先会对 var a = 2; 这段程序进行编译，然后做好执行它的准备，并且通常马上就会执行它。 

#### 分词/词法分析(Tokenizing/Lexing) 阶段

将`var a = 2;`变成 var、a、=、2和 ; 这样的词法单元，（python 中空格可能也是此法单元）

#####解析/语法分析(Parsing) 阶段

这个过程是将词法单元流(数组)转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树。这个树被称为“抽象语法树”(Abstract Syntax Tree，AST)。

把

```javascript
function fun(a, b) {}
```

转化成

```json
{
	"type": "FuctionDeclaration", // 函数声明
	"id": {
		"type": "Identifier", // 标识符
		"name": "fun" // 函数名称
	},
	"params": [ // 函数参数
		{
			"type": "Identifier",
			"name": "a" // 参数名称
		},
		{
			"type": "Identifier",
			"name": "b"
		}
	],
	"body": {
		"type": "BlockStatement",
		body: []
	}
}
```

#### 代码生成阶段

将 AST 转换为可执行代码的过程被称为代码生成。这个过程与语言、目标平台等息息相关。

`var a = 2;`会转化为一组机器指令，用来创建一个叫做 a 的变量（包括分配内存等)，并将一个值储存在 a 中。



### 理解作用域

#### 过程参与者

* **引擎**：从头到尾负责整个 Javascript 程序的编译以及执行过程。

* **编译器**：负责语法分析及代码生成。
* **作用域**：负责收集并维护由所有声明的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。

#### 过程

当你看见 `var a = 2;` 这段程序时，很可能认为这是一句声明。但我们的新朋友引擎却不这 么看。事实上，引擎认为这里有两个完全不同的声明，一个由编译器在编译时处理，另一个则由引擎在运行时处理。

下面我们将 var a = 2; 分解，看看引擎和它的朋友们是如何协同工作的。 

编译器首先会将这段程序分解成词法单元，然后将词法单元解析成一个树结构。但是当编译器开始进行代码生成时，它对这段程序的处理方式会和预期的有所不同。 

可以合理地假设编译器所产生的代码能够用下面的伪代码进行概括:“为一个变量分配内 存，将其命名为 a，然后将值 2 保存进这个变量。”然而，这并不完全正确。 

事实上编译器会进行如下处理。 

1. 遇到 var a，编译器会询问作用域是否已经有一个该名称的变量存在于同一个作用域的 集合中。如果是，编译器会忽略该声明，继续进行编译;否则它会要求作用域在当前作 用域的集合中声明一个新的变量，并命名为 a。 
2. 接下来编译器会为引擎生成运行时所需的代码，这些代码被用来处理 a = 2 这个赋值 操作。引擎运行时会首先询问作用域，在当前的作用域集合中是否存在一个叫作 a 的 变量。如果是，引擎就会使用这个变量;如果否，引擎会继续查找该变量(查看 1.3 节)。 

如果引擎最终找到了 a 变量，就会将 2 赋值给它。否则引擎就会举手示意并抛出一个异常! 

**总结**：变量的赋值操作会执行两个动作，首先编译器会在当前作用域中声明一个变量(如 果之前没有声明过)，然后在运行时引擎会在作用域中查找该变量，如果能够找到就会对 它赋值。 

#### 编译器有话说

LHS 去找到变量指针或者内存地址

RHS 去找到变量的值

编译器在编译过程的第二步中生成了代码，引擎执行它时，会通过查找变量 a 来判断它是 否已声明过。查找的过程由作用域进行协助，但是引擎执行怎样的查找，会影响最终的查 找结果。 

在我们的例子中，引擎会为变量 a 进行 LHS 查询。 

#### 引擎和作用域的对话

```javascript
function foo(a) {
	console.log( a + b);
}
var b = 2;
foo(2);
```



> 引擎:我说作用域，我需要为 foo 进行 RHS 引用。你见过它吗? 
>
> 作用域:别说，我还真见过，编译器那小子刚刚声明了它。它是一个函数，给你。 
>
> 引擎:哥们太够意思了!好吧，我来执行一下 foo。 
>
> 引擎:作用域，还有个事儿。我需要为 a 进行 LHS 引用，这个你见过吗? 
>
> 作用域:这个也见过，编译器最近把它声名为 foo 的一个形式参数了，拿去吧。 
>
> 引擎:大恩不言谢，你总是这么棒。
>
> 现在我要把 2 赋值给 a。
>
> 引擎:哥们，不好意思又来打扰你。我要为 console 进行 RHS 引用，你见过它吗? 作用域:咱俩谁跟谁啊，再说我就是干这个。
>
> 这个我也有，console 是个内置对象。 给你。 
>
> 引擎:么么哒。我得看看这里面是不是有 log(..)。太好了，找到了，是一个函数。
>
> 引擎:哥们，能帮我再找一下对 a 的 RHS 引用吗?虽然我记得它，但想再确认一次。
>
> 作用域:放心吧，这个变量没有变动过，拿走，不谢。
>
> 引擎：foo 的作用域兄弟，你对它进行 RHS见过 b 吗？我需要对它进行 RHS 引用。
>
> 作用域：听都没听过，走开。
>
> 引擎：foo 的上级作用域兄弟啊，咦？有眼不识泰山，原来你是全局作用域大哥，太好了。你见过 b吗？我需要对它进行 RHS 引用。
>
> 作用域：当然了，给你吧
>
> 引擎:真棒。我来把 a 的值，也就是 2，b 的值2，传递进 log(..)。 

#### 作用域嵌套

当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套。 

#### 异常

为什么区分 LHS 和 RHS 是一件重要的事情? 

因为在变量还没有声明(在任何作用域中都无法找到该变量)的情况下，这两种查询的行 为是不一样的。 

如果 RHS 查询在所有嵌套的作用域中遍寻不到所需的变量，引擎就会抛出 ReferenceError 异常。 

相较之下，当引擎执行 LHS 查询时，如果在顶层(全局作用域)中也无法找到目标变量， 

全局作用域中就会创建一个具有该名称的变量，并将其返还给引擎，前提是程序运行在非 “严格模式”下。 

在 严格模式中 LHS 查询失败时，并不会创建并返回一个全局变量，引擎会抛出同 RHS 查询 失败时类似的 ReferenceError 异常。 

接下来，如果 RHS 查询找到了一个变量，但是你尝试对这个变量的值进行不合理的操作， 比如试图对一个非函数类型的值进行函数调用，或着引用 null 或 undefined 类型的值中的 属性，那么引擎会抛出另外一种类型的异常，叫作 TypeError。 

ReferenceError 同作用域判别失败相关，而 TypeError 则代表作用域判别成功了，但是对 结果的操作是非法或不合理的。 

#### 小结

作用域是一套规则，用于确定在何处以及如何查找变量(标识符)。如果查找的目的是对变量进行赋值，那么就会使用 LHS 查询;如果目的是获取变量的值，就会使用 RHS 查询。 赋值操作符会导致 LHS 查询。=操作符或调用函数时传入参数的操作都会导致关联作用域 的赋值操作。 

JavaScript引擎首先会在代码执行前对其进行编译，在这个过程中，像var a = 2这样的声 明会被分解成两个独立的步骤: 

1. 首先，var a 在其作用域中声明新变量。这会在最开始的阶段，也就是代码执行前进行。 
2. 接下来，a = 2 会查询(LHS 查询)变量 a 并对其进行赋值。 

不成功的 RHS 引用会导致抛出 ReferenceError 异常。不成功的 LHS 引用会导致自动隐式 地创建一个全局变量(非严格模式下)，该变量使用 LHS 引用的目标作为标识符，或者抛 出 ReferenceError 异常(严格模式下)。 



## 2 词法作用域

在第 1 章中，我们将“作用域”定义为一套规则，这套规则用来管理引擎如何在当前作用 域以及嵌套的子作用域中根据标识符名称进行变量查找。 

作用域共有两种主要的工作模型。第一种是最为普遍的，被大多数编程语言所采用的词法 作用域。另外一种叫作动态作用域，仍有一些编程语 言在使用(比如 Bash 脚本、Perl 中的一些模式等)。 

### 2.1 词法阶段

大部分标准语言编译器工作的第一个阶段叫词法化，也叫单词化。词法化的过程对源代码中的字符进行检查。

词法作用域就是定义在此法阶段的作用域。就是在写代码时将变量和块作用域写在哪里决定的，词法分析器处理代码时会保持作用域不变（大部分情况是这样的）。

代码：

```javascript
function foo (a) {
	var b = a * 2;
	function bar (c) {
		console.log(a, b, c)
	}
	boo(2)
}
foo(2)
```



![image-20191116095305236](/Users/lvjiawen/Library/Application Support/typora-user-images/image-20191116095305236.png)

1 包含着整个全局作用域，其中只有一个标识符:foo。 

2 包含着 foo 所创建的作用域，其中有三个标识符:a、bar 和 b。 

3 包含着 bar 所创建的作用域，其中只有一个标识符:c。 

### 2.2 查找

作用域气泡的结构和互相之间的位置关系给引擎提供了足够的位置信息，引擎用这些信息 来查找标识符的位置。 

作用域查找会在找到第一个匹配的标识符时停止。在多层的嵌套作用域中可以定义同名的 标识符，这叫作“遮蔽效应”(内部的标识符“遮蔽”了外部的标识符)。 

作用域查找始终从运行时所处的最内部作用域开始，逐级向外或者说向上进行，直到遇见 第一个匹配的标识符为止。 

**无论函数在哪里被调用，也无论它如何被调用，它的词法作用域都只由函数被声明时所处 的位置决定。 **

词法作用域查找只会查找一级标识符，比如 a、b 和 c。如果代码中引用了 `foo.bar.baz`， 词法作用域查找只会试图查找 foo 标识符，找到这个变量后，对象属性访问规则会分别接 管对 bar 和 baz 属性的访问。 

### 2.3 欺骗词法

#### eval

```javascript
function foo(str, a) {
	eval( str ); // 欺骗! 
	console.log( a, b );
}
var b = 2;
foo( "var b = 3;", 1 ); // 1, 3
```

严格模式无法欺骗

```javascript
function foo(str) { 
	"use strict";
	eval( str );
	console.log( a ); // ReferenceError: a is not defined
}
foo( "var a = 2" );

```

#### with

```javascript
function foo(obj) {
	with (obj) {
	a = 2;
  }
}
var o1 = { a: 3 };
var o2 = { b: 3 };
foo( o1 );
console.log( o1.a ); // 2
foo( o2 );
console.log( o2.a ); // undefined
console.log( a ); // 2——不好，a 被泄漏到全局作用域上了!

```

with 本质上是通过将一个对象的引用当作作用域来处理，将对象的属性当作作 用域中的标识符来处理，从而创建了一个新的词法作用域(同样是在运行时)。 

### 2.4 性能

JavaScript 引擎会在编译阶段进行数项的性能优化。其中有些优化依赖于能够根据代码的 词法进行静态分析，并预先确定所有变量和函数的定义位置，才能在执行过程中快速找到 标识符。 

但如果引擎在代码中发现了 eval(..) 或 with，它只能简单地假设关于标识符位置的判断 都是无效的，因为无法在词法分析阶段明确知道 eval(..) 会接收到什么代码，这些代码会 如何对作用域进行修改，也无法知道传递给 with 用来创建新词法作用域的对象的内容到底 是什么。 

### 2.5 小结

词法作用域意味着作用域是由书写代码时函数声明的位置来决定的。 编译的词法分析阶段 基本能够知道全部标识符在哪里以及是如何声明的，从而能够预测在执行过程中如何对它 们进行查找。 

## 第三章 函数作用域和块作用域

正如我们在第 2 章中讨论的那样，作用域包含了一系列的“气泡”，每一个都可以作为容 器，其中包含了标识符(变量、函数)的定义。 这些气泡互相嵌套并且整齐地排列成蜂窝 型，排列的结构是在写代码时定义的。 

但是，究竟是什么生成了一个新的气泡?只有函数会生成新的气泡吗? JavaScript 中的其 他结构能生成作用域气泡吗? 

### 3.1 函数中的作用域

```javascript
function foo(a) {
	var b = 2;
	// 一些代码
	function bar() {
		// ...
	}
	// 更多的代码 
	var c = 3;
}
```

在这个代码片段中，foo(..) 的作用域气泡中包含了标识符 a、b、c 和 bar。无论标识符 声明出现在作用域中的何处，这个标识符所代表的变量或函数都将附属于所处作用域的气 泡。我们将在下一章讨论具体的原理。 bar(..) 拥有自己的作用域气泡。全局作用域也有自己的作用域气泡，它只包含了一个标 识符:foo。 

函数作用域的含义是指，属于这个函数的全部变量都可以在整个函数的范围内使用及复 用(事实上在嵌套的作用域中也可以使用)。 

### 3.2 隐藏内部实现

不合理

```javascript
function doSomething(a) {
  b = a + doSomethingElse( a * 2 );
  console.log( b * 3 );
}
function doSomethingElse(a) {
  return a - 1;
 }
var b;
doSomething( 2 ); // 15
```

在这个代码片段中，变量 b 和函数 doSomethingElse(..) 应该是 doSomething(..) 内部具体 实现的“私有”内容。给予外部作用域对 b 和 doSomethingElse(..) 的“访问权限”不仅 没有必要，而且可能是“危险”的，因为它们可能被有意或无意地以非预期的方式使用， 从而导致超出了 doSomething(..) 的适用条件。 

合理

```javascript
function doSomething(a) {
  var b = a + doSomethingElse( a * 2 );
  console.log( b * 3 );
  function doSomethingElse(a) {
    return a - 1;
  }
}
doSomething( 2 ); // 15
```

### 3.2.1 规避冲突

“隐藏”作用域中的变量和函数所带来的另一个好处，是可以避免同名标识符之间的冲突， 两个标识符可能具有相同的名字但用途却不一样，无意间可能造成命名冲突。 

#### 3.2.1.1 全局命名空间

库通常会在全局作用域中声明一个名字足够独特的变量，通常是一个对象。这个对象 被用作库的命名空间，所有需要暴露给外界的功能都会成为这个对象(命名空间)的属 性，而不是将自己的标识符暴漏在顶级的词法作用域中。 

```javascript
var MyReallyCoolLibrary = {
	awesome: "stuff",
	doSomething: function() {
		// ...
	},
	doSomethingElse: function() {
		// ...
	}
}
```

#### 3.2.1.2 模块管理

### 3.3 函数作用域

我们已经知道，在任意代码片段外部添加包装函数，可以将内部的变量和函数定义“隐藏”起来，外部作用域无法访问包装函数内部的任何内容。 

```javascript
var a = 2;

function foo() { // <-- 添加这一行
  var a = 3;
  console.log( a ); // 3
} // <-- 以及这一行

foo(); // <-- 以及这一行
console.log( a ); // 2
```

这种技术可以解决一些问题，但是它并不理想，因为会导致一些额外的问题。首先， 必须声明一个具名函数 foo()，意味着 foo 这个名称本身“污染”了所在作用域(在这个 例子中是全局作用域)。其次，必须显式地通过函数名(foo())调用这个函数才能运行其 中的代码。 

解决方案

```javascript
var a = 2;

(function foo(){ // <-- 添加这一行
	var a = 3;
	console.log( a ); // 3
})(); // <-- 以及这一行

console.log( a ); // 2
```

函数声明和函数表达式之间最重要的区别是它们的名称标识符将会绑定在何处。 

区分函数声明和表达式最简单的方法是看 function 关键字出现在声明中的位 置(不仅仅是一行代码，而是整个声明中的位置)。如果 function 是声明中 的第一个词，那么就是一个函数声明，否则就是一个函数表达式。 

比较一下前面两个代码片段。第一个片段中 foo 被绑定在所在作用域中，可以直接通过foo() 来调用它。第二个片段中 foo 被绑定在函数表达式自身的函数中而不是所在作用域中。 

换句话说，(function foo(){ .. })作为函数表达式意味着foo只能在..所代表的位置中 被访问，外部作用域则不行。foo 变量名被隐藏在自身中意味着不会非必要地污染外部作 用域。 

#### 3.3.1 匿名和具名

```javascript
setTimeout( function() {
	console.log("I waited 1 second!");
}, 1000 );
```

setTimeout 中的function 使用的也是函数表达式，是一种匿名函数表达式，，因为 function().. 没有名称标识符。

函数表达式可以是匿名的， 而函数声明则不可以省略函数名——在 JavaScript 的语法中这是非法的。

另一个函数需要引用自身的例子，是在事件触发后事件监听器需要解绑自身。 

始终给函数表达式命名是一个最佳实践。

```javascript
setTimeout( function timeoutHandler() { // <-- 快看，我有名字了! 
	console.log( "I waited 1 second!" );
}, 1000 );
```



#### 3.3.2 立即执行函数表达式

(function foo(){ .. })()。第一个 ( ) 将函数变成表 达式，第二个 ( ) 执行了这个函数。 

IIFE 还有一种变化的用途是倒置代码的运行顺序，将需要运行的函数放在第二位，在 IIFE 执行之后当作参数传递进去。这种模式在 UMD(Universal Module Definition)项目中被广 泛使用。尽管这种模式略显冗长，但有些人认为它更易理解。 

```javascript
var a = 2; 
(function IIFE( def ) {
	def( window ); 
})(**function** def( global ) { 
	var a = 3;
	console.log( a ); // 3
	console.log( global.a ); // 2 
}); 
```

函数表达式 def 定义在片段的第二部分，然后当作参数(这个参数也叫作 def)被传递进 IIFE 函数定义的第一部分中。最后，参数 def(也就是传递进去的函数)被调用，并将 window 传入当作 global 参数的值。 

### 3.4块作用域

```javascript
for (var i=0; i<10; i++) {
  console.log( "i:"+i );
}
console.log(i) // 10
```



```javascript
var foo = true;
if (foo) {
	var bar = foo * 2;
	bar = something( bar );
	console.log( bar );
}
```



#### 3.4.1 with

用 with 从对象中创建出的作用域仅在 with 声明中而非外 部作用域中有效。 

#### 3.4.2 try / catch

非常少有人会注意到 JavaScript 的 ES3 规范中规定 try/catch 的 catch 分句会创建一个块作用域，其中声明的变量仅在 catch 内部有效。 

#### 3.4.3 let

let 关键字可以将变量绑定到所在的任意作用域中(通常是 { 。。。}内部）为其声明的变量隐式地创建了所在的块作用域。 

使用 let 进行的声明不会在块作用域中进行提升。声明的代码被运行之前，声明并不 “存在”。 

##### 3.4.3.1 垃圾回收

```javascript
function process(data) {
	// 在这里做点有趣的事情
}
// 在这个块中定义的内容可以销毁了! 
{
	let someReallyBigData = { .. };
	process( someReallyBigData );
}
var btn = document.getElementById( "my_button" );
btn.addEventListener( "click", function click(evt){
	console.log("button clicked");
}, /*capturingPhase=*/false );
```



##### 3.4.3.2 let 循环

```javascript
for (let i=0; i<10; i++) {
	console.log( i );
}
console.log( i ); // ReferenceError

// 类似于
{
	let j;
	for (j=0; j<10; j++) {
		let i = j; // 每个迭代重新绑定!
		console.log( i );
  }
}
```

###3.5 小结

函数是 JavaScript 中最常见的作用域单元。本质上，声明在一个函数内部的变量或函数会在所处的作用域中“隐藏”起来，这是有意为之的良好软件的设计原则。 

## 4 提升

任何声明在 某个作用域内的变量，都将附属于这个作用域。 

直觉上会认为 JavaScript 代码在执行时是由上到下一行一行执行的。但实际上这并不完全正确，有一种特殊情况会导致这个假设是错误的。

```javascript
a = 2;
var a;
console.log( a ); //
```

编译之后变成

```javascript
var a;
a = 2;
console.log(a)
```

另外一段代码

```javascript
console.log( a ); // undefined
var a = 2;
```

编译之后变为

```javascript
var a;
console.log(a);
a = 2;
```



引擎会 在解释 JavaScript 代码之前首先对其进行编译。编译阶段中的一部分工作就是找到所有的 声明，并用合适的作用域将它们关联起来，这正是词法作用域 的核心内容。 

包括变量和函数在内的所有声明都会在任何代码被执行前首先 被处理。 

当你看到`var a = 2;`时，可能会认为这是一个声明。但 JavaScript 实际上会将其看成两个 声明:var a;和a = 2;。第一个定义声明是在编译阶段进行的。第二个赋值声明会被留在 原地等待执行阶段。 

这个过程就好像变量和函数声明从它们在代码中出现的位置被“移动” 到了最上面。这个过程就叫作提升。 

另外值得注意的是，每个作用域都会进行提升操作。 

函数声明会被提升，但是函数表达式却不会被提升。 

```javascript
foo(); // 不是 ReferenceError, 而是 TypeError!
var foo = function bar() { 
	/ ...
};
```

这段程序中的变量标识符 foo() 被提升并分配给所在作用域(在这里是全局作用域)，因此 foo() 不会导致ReferenceError。但是 foo 此时并没有赋值(如果它是一个函数声明而不 是函数表达式，那么就会赋值)。foo() 由于对 undefined 值进行函数调用而导致非法操作， 因此抛出 TypeError 异常。 

函数声明和变量声明都会被提升。但是一个值得注意的细节(这个细节可以出现在有多个“重复”声明的代码中)是函数会首先被提升，然后才是变量。 

```javascript
foo(); // 1
var foo;
function foo() {
	console.log(1)
}
foo = function() {
	coonsole.log(2)
}
```

会被解释成

```javascript
function foo() { 
	console.log( 1 );
}
foo(); // 1
foo = function() { 
	console.log( 2 );
};
```

注意，var foo 尽管出现在 function foo()... 的声明之前，但它是重复的声明(因此被忽 略了)，因为函数声明会被提升到普通变量之前。 

```javascript
foo(); // 3
function foo() {
	console.log( 1 );
}
var foo = function() {
	console.log( 2 );
};
function foo() { 
	console.log( 3 );
}

```

尽管重复的 var 声明会被忽略掉，但出现在后面的函数声明还是可以覆盖前面的。 

我们习惯将var a = 2;看作一个声明，而实际上JavaScript引擎并不这么认为。它将var a 和 a = 2 当作两个单独的声明，第一个是编译阶段的任务，而第二个则是执行阶段的任务。 

这意味着无论作用域中的声明出现在什么地方，都将在代码本身被执行前首先进行处理。 可以将这个过程形象地想象成所有的声明(变量和函数)都会被“移动”到各自作用域的 最顶端，这个过程被称为提升。 

## 作用域闭包

当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用 域之外执行。

```javascript
function foo() {
	var a = 2;
	function bar() {
		console.log( a ); // 2
	}
	bar();
}
foo();
```

函数 bar() 具有一个涵盖 foo() 作用域的闭包 (事实上，涵盖了它能访问的所有作用域，比如全局作用域) 。

```javascript
function foo() {
	var a = 2;
  function bar() {
    console.log( a );
  }
	return bar;
}
var baz = foo();
baz() // 2
```

在 foo() 执行后，其返回值(也就是内部的 bar() 函数)赋值给变量 baz 并调用 baz()，实 际上只是通过不同的标识符引用调用了内部的函数 bar()。 

bar() 显然可以被正常执行。但是在这个例子中，它在自己定义的词法作用域以外的地方执行。 

在 foo() 执行后，通常会期待 foo() 的整个内部作用域都被销毁，因为我们知道引擎有垃圾回收器用来释放不再使用的内存空间。由于看上去 foo() 的内容不会再被使用，所以很自然地会考虑对其进行回收。 

而闭包的“神奇”之处正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此没有被回收。谁在使用这个内部作用域?原来是 bar() 本身在使用。 

拜 bar() 所声明的位置所赐，它拥有涵盖 foo() 内部作用域的闭包，使得该作用域能够一直存活，以供 bar() 在之后任何时间进行引用。 

bar() 依然持有对该作用域的引用，而这个引用就叫作闭包。

```javascript
unction foo() {
	var a = 2;
  function baz() {
    console.log( a ); // 2
  }
	bar( baz );
 }
function bar(fn) {
	fn(); // 妈妈快看呀，这就是闭包!
}
```

```javascript
var fn;
function foo() {
	var a = 2;
	function baz() {
		console.log( a );
	}
	fn = baz; // 将 baz 分配给全局变量
}
function bar() {
	fn(); // 妈妈快看呀，这就是闭包!
}
foo();
bar(); // 2

```

无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用域的引用，无论在何处执行这个函数都会使用闭包。 

```javascript
function wait(message) {
	setTimeout( function timer() {
		console.log( message );
	}, 1000 );
}
wait( "Hello, closure!" );
```

将一个内部函数(名为 timer)传递给 setTimeout(..)。timer 具有涵盖 wait(..) 作用域的闭包，因此还保有对变量 message 的引用。

wait 函数执行之后，message 还是没有销毁。

 wait(..) 执行 1000 毫秒后，它的内部作用域并不会消失，timer 函数依然保有 wait(..)作用域的闭包。 

深入到引擎的内部原理中，内置的工具函数 setTimeout(..) 持有对一个参数的引用，这个参数也许叫作 fn 或者 func，或者其他类似的名字。引擎会调用这个函数，在例子中就是内部的 timer 函数，而词法作用域在这个过程中保持完整。 

这就是闭包。 

```javascript
function setupBot(name, selector) {
  $( selector ).click( function activator() {
    console.log( "Activating: " + name );
  });
}
setupBot( "Closure Bot 1", "#bot_1" );
setupBot( "Closure Bot 2", "#bot_2" );
```



本质上无论何时何地，如果将函数(访问它们各自的词法作用域)当作第一 级的值类型并到处传递，你就会看到闭包在这些函数中的应用。在定时器、事件监听器、 Ajax 请求、跨窗口通信、Web Workers 或者任何其他的异步(或者同步)任务中，只要使用了回调函数，实际上就是在使用闭包! 

### 循环和闭包

第 3 章介绍过，IIFE 会通过声明并立即执行一个函数来创建作用域。 

```javascript
for (var i=1; i<=5; i++) {
	(function() {
		var j = i
		setTimeout(function timer(){ 
			console.log( j );
    }, j*1000 );
	})();
}
```

仔细思考我们对前面的解决方案的分析。我们使用 IIFE 在每次迭代时都创建一个新的作用 域。换句话说，每次迭代我们都需要一个块作用域。第 3 章介绍了 let 声明，可以用来劫 持块作用域，并且在这个块作用域中声明一个变量。 

```javascript
for (let i=1; i<=5; i++) {
	setTimeout( function timer() {
		console.log( i );
	}, i*1000 );
}

```

### 模块

```javascript
function CoolModule() {
	var something = "cool"
	var another = [1, 2, 3]
	
	function doSomething () {
		console.log(something)
	}
	function doAnother() {
		console.log(another.join('!'))
	}
	return {
		doSomething: doSomething,
		doAnother: doAnother
	}
}

var foo = CoolModule()
foo.doSomething()
foo.doAnother()
```

公共 API 模式

```javascript
var foo = (function CoolModule(id) {
	function change() {
		// 修改公共 API
		publicAPI.identify = identify2;
  }
	function identify1() {
		console.log( id );
	}
	function identify2() {
		console.log( id.toUpperCase() );
	}
	var publicAPI = {
		change: change,
    identify: identify1
   };
	return publicAPI;
})( "foo module" );

foo.identify(); // foo module 
foo.change();
foo.identify(); // FOO MODULE
```

#### 现代的模块机制

```javascript
var MyModules = (function Manager() {
	var modules = {};
	function define(name, deps, impl) {
		for (var i=0; i<deps.length; i++) {
    	deps[i] = modules[deps[i]];
    }
		modules[name] = impl.apply( impl, deps );
	}
	function get(name) {
		return modules[name];
	}
	return {
		define: define,
		get: get
	};
})();

```

使用

```javascript
MyModules.define("bar", [], function() {
	function hello(who) {
		return "Let me introduce: " + who
	}
	return {
		hello: hello
	}
})

MyModules.define('foo', ['bar'], function(bar) {
	var hungry = 'hippo'
	function awesome() {
		console.log(bar.hello(hungry).toUpperCase())
	}
	return {
		awesI ome: awesome
	}
})

var bar = MyModules.get('bar')
var foo = MyModules.get('foo')

console.log(bar.hello('hippo'))
foo.awesome()
```

#### ES6 模块

ES6 中为模块增加了一级语法支持。但通过模块系统进行加载时，ES6 会将文件当作独立的模块来处理。每个模块都可以导入其他模块或特定的 API 成员，同样也可以导出自己的API 成员。 

基于函数的模块并不是一个能被稳定识别的模式(编译器无法识别)，它们的API语义只有在运行时才会被考虑进来。因此可以在运行时修改一个模块的 API(参考前面关于公共 API 的讨论)。 

比之下，ES6 模块 API 更加稳定(API 不会在运行时改变)。由于编辑器知道这一点，因此可以在(的确也这样做了)编译期检查对导入模块的 API 成员的引用是否真实存在。如果 API 引用并不存在，编译器会在运行时抛出一个或多个“早期”错误，而不会像往常一样在运行期采用动态的解决方案。 

ES6 的模块没有“行内”格式，必须被定义在独立的文件中(一个文件一个模块)。浏览器或引擎有一个默认的“模块加载器”(可以被重载，但这远超出了我们的讨论范围)可以在导入模块时异步地加载模块文件。 

### 小结

实际上闭包只是一个标准，是关于如何在函数作为值按需传递的词法环境中书写代码的。 

当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这时 就产生了闭包。 

模块有两个主要特征:(1)为创建内部作用域而调用了一个包装函数;(2)包装函数的返回值必须至少包括一个对内部函数的引用，这样就会创建涵盖整个包装函数内部作用域的闭包。 

## 动态作用域

词法作用域最重要的特征是它的定义过程发生在代码的书写阶段。

动态作用域并不关心函数和作用域是如何声明以及在何处声明的，只关心它们从何处调用。

词法作用域是在写代码或者说定义时确定的，而动态作用域是在运行时确定的。 词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用。 

```javascript
function foo() {
	console.log( a ); // 2
}
function bar() {
	var a = 3;
	foo();
}
var a = 2;
bar();

```

词法作用域让 foo() 中的 a 通过 RHS 引用到了全局作用域中的 a，因此会输出 2。 

而动态作用域并不关心函数和作用域是如何声明以及在何处声明的，只关心它们从何处调用。换句话说，作用域链是基于调用栈的，而不是代码中的作用域嵌套。因此，如果 JavaScript 具有动态作用域，理论上，上面代码中的 foo() 在执行时将会输出 3。 

## this 词法（这一节只能存疑）

ES6 添加了一个特殊的语法形式用于函数声明，叫作箭头函数。 它看起来是下面这样的: 

```javascript
var foo = a => {
	console.log( a );
};
foo( 2 ); // 2
```

下面的代码出现了问题

```javascript
var obj = {
	id: 'awesome',
	cool: function coolFn() {
		console.log(this.id)
	}
}
var id = 'not cool'

obj.cool()

setTimeout(obj.cool, 100); // not cool
```

解决方案 self = this

```javascript
var obj = {
	id: 'awesome',
	cool: function coolFn() {
		var self = this;
		function() {
		
		}
		console.log(self.id)
	}
}
var id = 'not cool'

obj.cool()

setTimeout(obj.cool, 100); // not cool
```

上面的方案 失败了

```javascript
var obj = {
	id: 'awesome',
	cool: () => {
		console.log(this.id)
	}
}
var id = 'not cool'

obj.cool()

setTimeout(obj.cool, 100); // error
```

上面的这个又失败了，下面这个成功了。

```javascript
var obj = {
	id: 'awesome',
	cool: function coolFn() {
		console.log(this.id)
	}
}
var id = 'not cool'

obj.cool()

setTimeout(() => {obj.cool()}, 100); // awesome
```


上面的又失败了

箭头函数在涉及 this 绑定时的行为和普通函数的行为完全不一致。它放弃了所 有普通 this 绑定的规则，取而代之的是用当前的词法作用域覆盖了 this 本来的值。 

这样除了可以少写一些代码，我认为箭头函数将程序员们经常犯的一个错误给标准化了， 也就是混淆了 this 绑定规则和词法作用域规则。 