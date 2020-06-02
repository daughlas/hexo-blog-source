---
title: JS 数据类型
date: 
categories:
- 一段代码
tags:
- javascript
---

# JS 数据类型

> 每个对象都有一个 toString() 方法，当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。默认情况下，toString() 方法被每个 Object 对象继承。如果此方法在自定义对象中未被覆盖，toString() 返回 "[object type]"，其中 type 是对象的类型。  ---MDN

事实上，比如Array、Date、RegExp等都重写了toString方法。为了防止或得到被覆盖的值，我们通常直接调用Object原型上未被覆盖的toString()方法，使用call来改变this指向来达到我们想要的效果。

```javascript
function isObject(target) {
    const type = typeof target;
    return target !== null && (type === 'object' || type === 'function');
}

function getType(target) {
  return Object.prototype.toString.call(target)
}

const mapTag = '[object Map]';
const setTag = '[object Set]';
const arrayTag = '[object Array]';
const objectTag = '[object Object]';

const boolTag = '[object Boolean]';
const dateTag = '[object Date]';
const errorTag = '[object Error]';
const numberTag = '[object Number]';
const regexpTag = '[object RegExp]';
const stringTag = '[object String]';
const symbolTag = '[object Symbol]';

```

|代码|结果|
|:--| :--|
|Object.prototype.toString.call(true)|"[object Boolean]"|
|Object.prototype.toString.call(123)|"[object Number]"|
|Object.prototype.toString.call("string")|"[object String]"|
|Object.prototype.toString.call(null)|"[object Null]"|
|Object.prototype.toString.call(undefined)|"[object Undefined]"|
|Object.prototype.toString.call(Symbol())|"[object Symbol]"|
|Object.prototype.toString.call({})|"[object Object]"|
|Object.prototype.toString.call(function(){})|"[object Function]"|
|Object.prototype.toString.call([])|"[object Array]"|
|Object.prototype.toString.call(new Error())|"[object Error]"|
|Object.prototype.toString.call(new RegExp())|"[object RegExp]"|
|Object.prototype.toString.call(Math)|"[object Math]"|
|Object.prototype.toString.call(JSON)|"[object JSON]"|
|Object.prototype.toString.call(document)|"[object HTMLDocument]"|
|Object.prototype.toString.call(window)|"[object Window]"|
