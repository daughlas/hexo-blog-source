---
title:  TypeScript(7) - 接口 Interface
date: 2019-08-25 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍 typeScript 中的接口

<!--more-->

# 简介
* 接口，英文 Interface
* 有两个作用：
  * 对对象的形状 shape 进行描述，规范和契约，规定一个类长成什么样子
  * 对 类 class 进行抽象
  * Duck Typing 鸭子类型，动态编程语言的一种对象推断策略，只要它走路像鸭子，叫起来像鸭子，那么他就是个鸭子



# 描述对象的形状
对象要和接口保持形状一致，属性不能多也不能少，而且属性的类型要鱼接口已知。
```typescript
interface Person {
  name: string
  age: number
}

let lvjiawen: Person  = {
  name: 'lvjiawen',
  age: 20,
}

// let lvjiawen: Person  = {
//   name: 'lvjiawen',
// } // error 不能少

// let lvjiawen: Person  = {
//   name: 'lvjiawen',
//   age: 20,
//   gender: 'man'
// } // error 不能多

```

接口还可以对属性的类型进行更灵活的配置

## 可选属性
可有可无的属性，在定义接口时，可以在属性名后边加一个 `?`，表示这个属性可以不定义，但是要是定义的话，类型依然需要与接口中的类型保持一致。

```typescript
interface Square {
    color: string
    area: number
}

interface SquareConfig {
    color?: string
    width?: number
}

function createSquare(config: SquareConfig) : Square {
    let newSquare = { color: 'black', area: 100 }
    if (config.color) {
        newSquare.color = config.color
    }
    if (config.width) {
        newSquare.area = config.width * config.width
    }
    return newSquare
}

let mySquare = createSquare({color: 'red'})
```

## 只读属性
有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 readonly 定义只读属性：

```typescript
interface Point {
    readonly x: number
    readonly y: number
}

let p1: Point = {x: 0, y: 0}
// p1.x = 50 // error，Attempt to assign to const or readonly variable 
```
readonly 与 const 的区别
* readonly 用在 属性上
* const 用在变量上


# 对类的行为进行抽象
在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implements）。

typescript 中的接口并不像 java 一样，需要一个类去实现。typescript 的接口只检查外形，外形符合就可以了。

typescript 中一个类只能继承一个类

```typescript
interface Radio {
  switchRadio() :void;
}

interface Battery {
  checkBatteryStatus():void;
}

class Car implements Radio  {
  switchRadio() {}
}

class CellPhone implements Radio, Battery {
  switchRadio() {}
  checkBatteryStatus() {}
}
```

