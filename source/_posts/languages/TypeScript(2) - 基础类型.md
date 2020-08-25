---
title:  Typescript(2) - 基本类型和变量声明
date: 2019-08-13 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍一下 es 的数据类型和 typescript 的各种原始类型及其用法。

<!--more-->

和 javascript 类似，typescript 的数据类型也可以分为：
* 原始类型、原始值、primitive values
  * 除 Object 意外所有类型都是
  * 值本身无法改变，就是说，操作原始类型一定会返回一个新的原始类型
  * ES 6 的基本数据类型
    * Boolean
    * Null
    * Undefined
    * Number
    * Bigint
    * String
    * Symbol
* Object

# 基本类型

## 布尔值

```typescript
const isLoading: boolean = false;
```

## 数字
number 要小写，大写是 interface

```typescript
const decLiteral: number = 6;
const decNum: number = 20; // 十进制
const hexNum: number = 0x14 // 十六进制
const binaryNum: number = 0b10100 // 二进制
const octalNum: number = 0o24 // 八进制
```

## 字符串

```typescript
const book : string = '介绍下 typescript';
const tmplString: string = `hello ${book}`
```

## void、null、undefined

undefined 和 null 是所有类型的子类型，他们可以给任何类型的变量赋值
```typescript

let c : undefined = undefined;
let d : null = null;
let num: number = undefined

```

# 变量声明

## let 和 const
* let 和 const 在一对会在一堆 {} 之内会创建一个新的块级作用域
* 块级作用域不能在声明之前读写，let、const 不行，var 可以
* 在同一个作用域内，或者作用域内不，let 和 const 不能重复声明同名变量
* const 不能修改，但是如果 const 是一个对象，只修改对象的属性是可以的，因为修改成员不修改引用
* 大部分情况下，应该多使用 const，除非确定一个结构要修改

## 解构和展开
等同于 ES6 中的解构和展开语法，可以看之前的文章
展开的时候，相当于做了一个浅拷贝
还有一点需要注意，对象展开的时候前展开和后展开是不一样的
```typescript
const originData: object = {
    a: 1,
    b: 2,
    c: 3
}
const new1 = {a: 8, ...originData}
const new2 = {...originData, a: 8}
console.log(new1) // { a: 1, b: 2, c: 3 }
console.log(new2) // { a: 8, b: 2, c: 3 }
```

```javascript
function test(isInBlock, x) {
  if (isInBlock) {
    let x = 100
    return x
  }
  return x
}
test(false, 0) // 0
test(true, 0) // 100
```