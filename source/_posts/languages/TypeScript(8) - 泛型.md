---
title:  TypeScript(8) - 泛型
date: 2019-08-25 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍 typeScript 中的泛型的用法

<!--more-->
泛型的英文叫 Generics，泛型的出现解决了什么问题

# 为什么需要泛型

下面我们来看一个需求演化过程：

```typescript
function echo(arg) {
  return arg
}
const result = echo(123)
```
上面的代码的问题，result 的类型就变成了 any，为了解决这个问题，给函数声明加变量类型

```typescript
function echo(arg: number): number {
  return arg
}
const result = echo(123)
```
此时 result 就变成了 number，但是此时我需要拓展，arg 可以传 string、boolean、对象，各种类型，之前的知识，我们解决的办法是：

```typescript
function echo(arg: any): any {
  return arg
}
const result = echo(123)
```
但是返回的 result 又变成了 any。
此时引入一个新的概念**泛型**，泛型是指在定义函数、接口、类的时候，我们不预先指定具体的类型，而是在使用的时候再指定类型。

```typescript
function echo<T>(arg: T): T {
  return arg
}
const result = echo('str')
// result = 4 // error
// const result: number = echo('str') // error
```

多个类型的泛型

```typescript
function swap(tuple) {
  return [tuple[1], tuple[0]]
}

const result = swap(['string', 1233])
result[0] = 'hello'
result[0] = true
```
这样就丧失了类型，改进办法还是使用 typescript

```typescript
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]]
}

const result = swap(['string', 1233])
// result[0] = 'hello' // error
```

# 约束泛型
是泛型只接受允许 length 属性的变量，初级，只允许数组
```typescript
function echoWithArr<T>(arg : T[]): T[] {
  console.log(arg.length)
  return arg
}

const arrs = echoWithArr([1, 2, 3])
```
这个方式不能传入 string，string 也是具有 length 的

```typescript
interface IWithLength {
  length: number
}

function echoWithLength<T extends IWithLength>(arg: T) : T {
  console.log(arg.length)
  return arg
}

const arrs2 = echoWithLength([1, 2, 3,])
const arrs3 = echoWithLength('hello world')
```

# 类和接口的泛型
问题：新建队列类，实现两个方法，进入队列，弹出队列
```typescript
class Queue {
  private data = [];

  push(item) {
    return this.data.push(item);
  }

  pop() {
    return this.data.shift();
  }
}

const queue = new Queue();
queue.push(1);
queue.push('str');
console.log(queue.pop().toFixed());
console.log(queue.pop().toFixed());
```
这个队列可以添加任意类型的数据，弹出的也是 any 类型的数据，编译，无法捕捉到错误，运行时会报错。
改进一：假设只能入队出队数字

```typescript
class Queue {
  private data = [];

  push(item: number) {
    return this.data.push(item);
  }

  pop(): number {
    return this.data.shift();
  }
}

const queue = new Queue();
queue.push(1);
queue.push('str'); // error
console.log(queue.pop().toFixed());
console.log(queue.pop().toFixed());
```
这样做的问题：想要写一个字符串的队列的时候，又要重新写一个类

```typescript
class Queue<T> {
  private data = [];

  push(item: T) {
    return this.data.push(item);
  }

  pop(): T {
    return this.data.shift();
  }
}

const queue = new Queue<number>();
queue.push(1);
// queue.push('str'); // error
console.log(queue.pop().toFixed());

const queue2 = new Queue<string>();
queue2.push('hello');
// queue.push('str'); // error
// console.log(queue2.pop().toFixed()); // error
```
多个类型的时候咋么办呢
```typescript
interface KeyPair<T, U> {
  key: T;
  value: U
}

let kp1: KeyPair<number, string> = {key: 1, value: 'hello'}
let kp2: KeyPair<number, string> = {key: 'abc', value: 1} // error
```

Array 是个 interface，在使用泛型，所以可以这样定义数组
```typescript
let arr: Array<number> = [1, 2, 3]
```

用 interface 描述函数的类型
```typescript
interface IPlus {
  (a: number, b: number) : number
}


function add(a: number, b: number): number {
  return a + b;
}

const a: IPlus = add
```

再进一步，函数支持泛型
```typescript
interface IPlus<T> {
  (a: T, b: T) : T
}


function add(a: number, b: number): number {
  return a + b;
}

function connect(a: string, b: string) : string {
  return a + b;
}
const a: IPlus<number> = add
const b: IPlus<string> = connect
```