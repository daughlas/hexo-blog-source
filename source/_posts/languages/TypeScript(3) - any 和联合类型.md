---
title:  Typescript(3) - any 和联合类型
date: 2019-08-14 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍类型不确定的时候，两种解决方案：any 和 联合类型

<!--more-->

如果我们不知道变量的类型的时候我们怎么办呢?typescript 有两种解决方案：any 和 联合类型，瞎main分别介绍他们：

# any

对于无法确定类型的变量，我们需要 any 类型，他表示允许赋值为任意类型，而且可以任意的调用属性和方法，基本上退化为 javascript，丧失了类型检查的功能。

对 any 类型的任何操作，返回的也还都是 any

```typescript
let notSure: any = 4

notSure = 'now I am a string' // OK

notSure = false // OK

notSure.myName // OK
notSure.getName() // OK
```

# never
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

# 联合类型

还有一种联合类型，可以将变量设置为特定几种中的一种，例如 `let myFavoriteNumber: string | number;`，语法是在多种类型中间加一个 ｜，**注意，这样的定义只能访问到它们共有的类型和方法。**

```typescript
let numberOrString: number | string = 234 // OK
numberOrString = 'abc' // OK
numberOrString = true // error
```

# 强制类型转换
如果需要强制转换类型的话，可以使用下边的语法；

```typescript
let someString: any
someString.split(',')
let someLength1: number = (<string>someString).length
let someLength2: number = (someString as string).length
```