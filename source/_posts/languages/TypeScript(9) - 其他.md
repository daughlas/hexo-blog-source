---
title:  TypeScript(9) - 其他
date: 2019-08-25 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍 typeScript 中的其他知识，包括类型别名、

<!--more-->
# 类型别名
type aliases

```typescript
function sum(a: number, b: number): number {
  return a + b;
}

const sum2: (s: number, y: number) => number  = sum;
```
这样写起来很麻烦，可以使用 类型别名简化

```typescript
type plusType =  (x: number, y: number) => number

function sum(a: number, b: number): number {
  return a + b;
}

const sum2: plusType = sum;
```
下面设想一个场景，如果入參是字符串，返回字符串；如果入參是函数，则返回函数的执行结果

```typescript
type NameResolver = () => string
type NameOrResolver = string | NameResolver
function getName(n: NameOrResolver): string {
  if (typeof n === 'string') {
    return n
  } else {
    return n()
  }
}
```

# 类型断言
与类型推断对应，当typescript不确定一个联合类型的变量是哪个类型的时候，我们只能访问所有联合类型里边公用的属性或者方法。
如果，我们需要在还不确定类型的时候，就访问类型的属性和方法。此时类型断言登场，它告诉编译器，你比他更了解这个类型，编译器不要再发出错误了。

```typescript
function getLength(input: string | number): number {
  const str = input as String;
  if (str.length) {
    return str.length;
  } else {
    const number = input as Number;
    return number.toString().length;
  }
}
```
还有一种简便的写法：
```typescript
function getLength(input: string | number): number {
  if ((<string>input).length) {
    return (<string>input).length;
  } else {
    return input.toString().length;
  }
}
```

类型断言不是类型转换，断言成一个非联合类型的类型是回报的。

# 声明文件
当使用第三方库的时候，需要引入它的声明文件，才能获得对应的代码补全，接口提示等等功能。

以 jqeury 为例
```typescript
declare var jQuery: (selector: string) => any
```
这样我们就可以使用 `jQuery` 了，通常我们会把所有的这些内容放到一个文件中，比如叫 `jQuery.d.ts`，我们叫他声明文件，以 .d.ts 结尾，typescript 会解析项目中的所有声明文件，然后项目中所有的文件就都可以获取 jQuery 这个定义了

```typescript
declare var jQuery: (selector: string) => any
```

如果不行，配置 tsconfig.json
```json
{
  "include": ["**/*"]
}
```
通常社区或者官方会为我们写好声明文件，例如 @types/jquery 这个 npm 包，就不用自己写声明文件了，一般的常见库都有，可以在网站 microsoft.github.io/TypeSearch 查找

如果一个库，没有提供声明文件，我们就需要自己书写声明文件