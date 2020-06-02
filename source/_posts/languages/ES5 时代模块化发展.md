---
title: ES5 模块化发展历程
date: 
categories:
- 编程语言
tags:
- 编程语言
- javascript
---

# 原始写法

```js
function m1() {
    // ....
}

function m2() {
    // ....
}

```

## 优点

## 缺点

1. 污染了全局变量
2. 容易导致明明冲突
3. 模块成员之间看不出直接关系

# 对象写法

```js
var module1 = new Object({
    _count: 0,
    m1: function() {
        //...
    },
    m2: function() {
        //...
    }
})


```

## 缺点

1. 暴露所有模块的成员
2. 内部状态可以被外部改写，比如 `module1._count = 5;` 就会直接改变内部计数器的值。

# 立即执行函数的写法

```js
var module1 = (function() {
    var _count = 0;
    var m1 = function() {
        // ...
    };
    var m2 = function() {
        // ...
    };

    return {
        m1: m1,
        m2: m2
    }
})();

```

## 优点

不暴露私有成员

# 放大模式

```js
var module1 = (function(mod) {
    mod.m3 = function() {
        // ...
    };

    return mod
})(module1)


```

上面的代码魏 module1 添加了一个新方法 m3()，然后返回新的 module1 模块。

由于模块可能是从网上获取的，不能确定哪个部分先加载完成，要采用宽放大模式。

```js
var module1 = (function(mod) {
    mod.m3 = function() {
        // ...
    };

    return mod
})(module1 || {})

```

# 输入全局变量

独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。为了在模块内部调用全局变量，必须显式地将其他变量输入模块。

```js
var module1 = (function($, YAHOO) {
    // ...
})(jQuery, YAHOO0;)

```

