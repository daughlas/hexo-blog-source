---
title:  TypeScript(5) - 函数
date: 2019-08-28 11:12:38
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍 typeScript 中的函数的写法

<!--more-->
函数声明的写法:
ts 的函数定义了两个必传参数的话，调用的时候也必须传递两个参数，不能多，也不能少。
函数传递可选参数的时候，用 `?` 的可选参数，调用函数的时候可以不传，可选参数必须接在必需参数后面。
```javascript
function add(x: number, y:number, z?:number):number {
  if (typeof z === 'number') {
    return x + y + z;
  }
  return x + y;
}

let result = add(2, 3);
let result2 = add(2, 3, 4);
// let result3 =add(2, 3, 4, 5); // error
// let result4 = add(2, 'a'); // error
```

声明类型，声明函数会自动推 add 是函数的类型。
若果要输血函数的类型的话,用=> 来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。
```typescript
const add = function add(x: number, y:number, z?:number): number {
  if (typeof z === 'number') {
    return x + y + z;
  }
  return x + y;
}

let result = add(2, 3);
let result2 = add(2, 3, 4);
// let result3 =add(2, 3, 4, 5); // error
// let result4 = add(2, 'a'); // error

// const add2:string = add // error
const add3: (x: number, y:number, z?:number) => number = add
```

类型推断不只适用于函数类型，其他类型也适用，下面的这段代码，会自动推断 str 为 string 类型，再将其赋值为一个 number 的时候就报错了
```javascript
let str = 'string'
// str = 123 // error
```

还可以使用方法定义函数的类型
```typescript
interface addFunc {
  (x: number, y:number, z?:number): number
}
const add : addFunc = function add(x: number, y:number, z?:number): number {
  if (typeof z === 'number') {
    return x + y + z;
  }
  return x + y;
}
```