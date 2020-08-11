---
title:  TypeScript 简介
date: 2017-08-12 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍一下 typescript 的基础，目的是满足 angular2.x 。

<!--more-->

## 基本类型

typescript 的类型包括：
* 布尔值
* 数字
* 字符串
* 数组
* 元组
* 枚举
* any
* void
* null 和 undefined
* never
* object

### 布尔值
```typescript
const isLoading: boolean = false;
```

### 数字

```typescript
const decLiteral: number = 6;
const decNum: number = 20; // 十进制
const hexNum: number = 0x14 // 十六进制
const binaryNum: number = 0b10100 // 二进制
const octalNum: number = 0o24 // 八进制
```

### 字符串

```typescript
const book : string = '介绍下 typescript';
const tmplString: string = `hello ${book}`
```

### 数组

数组可以通过两种方式定义：
1. 数组可以用「类型 + 方括号」表示法
2. 也可以使用数组泛型（Generic） Array<elemType> 来表示数组
3. 标示了类型之后，数组中不允许出现其它类型

```typescript
// 使用泛型定义数组有两种方式
// 方式一
const list1: Array<number> = [1, 2, 3]
// 方式二
const list2 : number[] = [2, 3, 4]
```

### 元组

元组，英文叫 tuple，就是一个已知数据类型和个数的数组。
元组类型不能访问越界元素。

```typescript
// 元组
let x: [string , number];
x = ['hello', 10] // ok
x = [10, 'hello'] // error
```

### 枚举

```typescript
// 枚举
const enum Direction {
  Up = 'Up',
  Down = 'Down',
  Left = 'Left',
  Right = 'Right'
}
```

### any
typescript 中也可以声明一些尚不明确类型的变量，又希望编译器编译通过的时候，可以使用 any，但不要滥用，否则 typescript 就失去了意义。

```typescript
let notSure : any = 1;
notSure = 'i am not a string'
```

### void、null、undefined


声明一个 void 类型的变量，只能赋值为 void 或 null，通常没意义。但是当函数没有返回值的时候可以将函数返回值类型设置为 void。
undefined 和 null 与 void 类似

```typescript
let a : void = undefined;
let b : void = null;

let c : undefined = undefined;
let d : null = null;

function hello() :void {
  console.log('world')
}
```

### never

还有 never 也是任何值的子类型，一般用于处理异常或者无限循环。
```typescript
function error(message: string): never {
  throw new Error(message)
}

function fail() {
  return error('something failed')
}

function infiniteLoop(): never {
  while(true) {

  }
}
```

### object

object 非原始类型，跟 js 中的 object 差不多。

```typescript
declare function test(o: object | string) : void

test({hello: 'world'}) // ok
test('hello') // ok
// test(1) // error
```

### 联合类型
还有一种联合类型，可以将变量设置为特定几种中的一种，例如 `let myFavoriteNumber: string | number;`，语法是在多种类型中间加一个 ｜，**注意，这样的定义只能访问到它们共有的类型和方法。**

### 强制类型转换
```typescript
let someString: any
someString.split(',')
let someLength1: number = (<string>someString).length
let someLength2: number = (someString as string).length
```



## 变量声明
### let 和 const
* let 和 const 在一对会在一堆 {} 之内会创建一个新的块级作用域
* 块级作用域不能在声明之前读写，let、const 不行，var 可以
* 在同一个作用域内，或者作用域内不，let 和 const 不能重复声明同名变量
* const 不能修改，但是如果 const 是一个对象，只修改对象的属性是可以的，因为修改成员不修改引用
* 大部分情况下，应该多使用 const，除非确定一个结构要修改

### 解构和展开
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

## 接口
再介绍一下 typescript 中的接口,typescript中的接口，规定了对象的属性以及属性的类型，还可以规定对象中方法的类型及返回类型。?表示可选属性，readonly 表示只读属性。

在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implements）。

typescript 中的接口并不像 java 一样，需要一个类去实现。typescript 的接口只检查外形，外形符合就可以了。

### 接口初探

```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
};

let p: Person = {
    id: 89757,
    name: 'Xcat Liu',
    website: 'http://xcatliu.com'
};

p.id = 9527;
```
上面的例子中，我们定义了一个接口 Person，接着定义了一个变量 p，它的类型是 Person。这样，我们就约束了 p 的形状必须和接口 Person 一致。定义的变量比接口少了一些属性是不允许的：属性多了也不行，反正是要一样（如果 `age: 25` 改成 `age?: 25` 就可以让形状不完全一致了可以不存在，但是还是不允许添加不存在的属性）。

下面具体介绍使用接口的一些具体方式：
### 可选属性
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
在 ES6 中，可以给函数的参数添加默认值， TypeScript 会将添加了默认值的参数识别为可选参数

这个时候，不再必须让可选参数必须放在必需参数后面

```typescript
function buildName(firstName: string, lastName: string = 'Liu') {
    return firstName + '' + lastName;
}

let scatliu = buildName('Xcat', 'Liu');
let xcat = buildName('Xcat');
```

### 只读属性
有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 readonly 定义只读属性：

```typescript
interface Point {
    readonly x: number
    readonly y: number
}

let p1: Point = {x: 0, y: 0}
// p1.x = 50 // error，Attempt to assign to const or readonly variable 
```
泛型只读数组
```typescript
let a: number[] = [1, 2, 3]
let ro: ReadonlyArray<number> = a
ro[0] = 1 // error， Index signature in type 'readonly number[]' only permits reading.
ro.push(1) // error，Property 'push' does not exist on type 'readonly number[]'.
```

### 额外属性检查
对象字面粱先赋值给一个对象，再将对象传递，就会跳过接口的类型检查
### 函数类型

ts 定义函数的两种方式，定义了两个参数，调用的时候也必须传递两个参数，不能多，也不能少。

在 TypeScript 的类型定义中，=> 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。两种方式：
方式一：
```typescript
interface SearchFunc {
  (source: string, subString: string): boolean
}

let mySearch: SearchFunc = (src: string, sub: string): boolean => {
  let ret = src.search(sub)
  return ret > -1
}
```
方式二：
```typescript
// 函数声明
function sum(x: number, y: number): number {
  return x + y;
}

// 函数表达式
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
  return x + y;
};
```

函数传递可选参数的时候，用 `?` 的可选参数，调用函数的时候可以不传，可选参数必须接在必需参数后面。

```typescript
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + ' ' + lastName;
  } else {
    return firstName;
  }
}
let p1 = buildName('Xcat', 'Liu');
let p2 = buildName(undefined, 'Xcat');
```
### 类类型
```typescript
interface DogInterface {
    name: string
    bark()
}

class Dog implements DogInterface {
    name: string

    constructor(name: string) {
    }
    
    bark() {}
}
```

### 总结
看一个综合一点的例子

```typescript
interface Phone {
  [name: string]: string
}

interface User {
  name: string
  age?: number
  readonly isMale: boolean
  phone: Phone
  say: (words: string) => string
}

const user: User = {
  name: '小红',
  age: 18,
  isMale: false,
  say: s => s + 'hello',
  phone: {
    brand: 'xiaomi',
    number: '13323235487'
  }
}

const getUserName = (user: User) => user.name
```