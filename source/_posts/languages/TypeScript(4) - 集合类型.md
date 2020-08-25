---
title:  TypeScript(4) - 集合类型
date: 2019-08-20 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍 typeScript 中的几种集合类型 Tuple、List、Set、Enum

<!--more-->

从此开始介绍原始类型之外的其他数据类型，首先介绍几种 typescript 中常见的集合。

# 数组 - Array

数组可以通过两种方式定义：
1. 数组可以用「类型 + 方括号」表示法
2. 也可以使用数组泛型（Generic） Array<elemType> 来表示数组
3. 标示了类型之后，数组中不允许出现其它类型

```typescript

let arrOfNums: number[] = [1, 2, 3]
// let arrOfNums: Array<number> = [1, 2, 3] // 同样效果

// arrOfNums = [1, 2, 3, '5'] // error
arrOfNums.push(3)
// arrOfNums.push('a') // error
```

类数组
* dom node 是 Array<Node>
* function de arguments 是 Array<IArguments>

# 元组 - Tuple

元组，英文叫 tuple,就是一个已知数据类型和个数的数组。他与数组的区别是数组通常存放同种数据，元组可以存放不同类型的数据。

元组类型不能访问越界元素。

```typescript
let user: [string, number] = ['lvjiawen', 20]
// user = ['lvjiawen', 20, true] // error
// let user: [string, number] = [20, 'lvjiawen'] // error
```

# Set
set 主要是 es6 的一种新的数据结构，Set 可以简单理解为是去重之后的数组

```javascript
// 去重
const arr = [1, 1, 2, 2];
const arr2 = [...new Set(arr)];
console.log(arr2); // [1, 2]

// 判断元素是否在集合中
const set = new Set(arr);
const has1 = set.has(1);
const has3 = set.has(3);
console.log(has1, has3); // true false


// 求交集
const set2 = new Set([2, 3]);
const set3 = new Set([...set].filter(item => set2.has(item)));
console.log(set3.size); // 1
```

# 枚举
## 数字枚举

```typescript
enum Direction {
  Up,
  Down,
  Left,
  Right
}

console.log(Direction.Up); // 0
console.log(Direction[0]); // Up

console.log(Direction.Left); // 2
console.log(Direction[3]); // Left
```

手动给一个枚举赋值
```typescript
enum Direction {
  Up = 10,
  Down,
  Left,
  Right
}

console.log(Direction.Up); // 10
console.log(Direction[0]); // Up

console.log(Direction.Left); // 12
console.log(Direction[3]); // Left
```

编译出来的代码是这样的，首先 执行 `Direction["Up"] = 10`，那么Direction["Up"] 的值变成10了，同时返回了一个 10，作为这个表达式的结果，则 `Direction[Direction["Up"] = 10] = "Up";` 就变成了 `Direction[10] = "Up";`
```javascript
var Direction;
(function (Direction) {
    Direction[Direction["Up"] = 10] = "Up";
    Direction[Direction["Down"] = 11] = "Down";
    Direction[Direction["Left"] = 12] = "Left";
    Direction[Direction["Right"] = 13] = "Right";
})(Direction || (Direction = {}));
```

## 字符串枚举
``` typescript
enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT'
}

const value = 'UP'
console.log(value === Direction.Up)
```

## 常量枚举
可以提升性能，只要加一个 const 就可以了
```typescript
const enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT'
}

const value = 'UP'
console.log(value === Direction.Up)
```

会被编译成下面这样

```javascript
var value = 'UP';
console.log(value === "UP" /* Up */);
```

注意，这个方式只能简化枚举的常量值，不能简化计算值类型的枚举
