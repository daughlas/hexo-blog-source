---
title: javascript 奇淫巧技
date: 
categories:
- 编程语言
tags:
- 编程语言
- javascript
---
想要从 arr 得到 arr2 的两种方法
```javascript
var arr=[
    [11,12,13,"内容","内容2"],
    [21,22,23,"内容","内容2"],
    [31,32,33,"内容","内容2"]
];
var arr2=[
    [11,12,13],
    [21,22,23],
    [31,32,33]
];
var arr2 = arr.map(cur => cur.slice(0, 3))
arr2 = Array.from(arr, item => item.slice(0, 3))
```

js 对象如何优雅的去一个深度的值

```javascript
let obj = {
    school: {
      class1: {
        student: 50
      }
    }
}

// 土法
let student = obj?(obj.school?(obj.school.class1?(obj.school.class1.studnet?obj.school.class1.studnet:''):''):''):'';

// 高级一
function getter(target, exec = '_') {
  return new Proxy({}, {
    get: (o, n) => {
      return n === exec ?
        target :
        getter(typeof target === 'undefined' ? target : target[n], exec)
    }
  });
}
console.log(getter(obj).school.class1.student._)//50
console.log(getter(obj).school1.class11.student._)//undefined

// 高级二
function safeProps(func, defaultVal) {
    try {
        return func();
    } catch(e) {
        return defaultVal;
    }
}
safeProps(func() {
    student = obj.school.class1.student
}, -1)

```

判断类型

```javascript
var types = ["Array", "Boolean", "Date", "Number", "Object", "RegExp", "String", "Window", "HTMLDocument"];

function isType(obj, type) {
  return Object.prototype.toString.call(obj) == "[object " + type + "]";
}

Object.prototype.toString.call(obj) == "[object " + type + "]";
```

## window.onload 和 $(document).ready() 

| `window.onload` | `$(document).ready()`                              |                                                            |      |
| --------------- | -------------------------------------------------- | ---------------------------------------------------------- | ---- |
| 执行时间        | 必须等待网页中所有的内容加载完毕（）包括图片才执行 | 只需要 DOM 加载完就执行（不包括图片等）                    |      |
| 编写个数        | 不能同时编写多个                                   | 能同时编写多个                                             |      |
| 简化写法        | 无                                                 | $(document).ready(function(){}) 可以简写成 $(function(){}) |      |

## 精确获取页面元素位置的方式有哪些

那就是使用getBoundingClientRect()方法。它返回一个对象，其中包含了left、right、top、bottom四个属性，分别对应了该元素的左上角和右下角相对于浏览器窗口（viewport）左上角的距离。

```
var X= this.getBoundingClientRect().left;
var Y =this.getBoundingClientRect().top;
//再加上滚动距离，就可以得到绝对位置
var X= this.getBoundingClientRect().left + document.documentElement.scrollLeft;
var Y =this.getBoundingClientRect().top + document.documentElement.scrollTop;
```

