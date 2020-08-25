---
title:  TypeScript(1) - 简介
date: 2019-08-12 14:24:20
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍一下 typescript 的好处，如何使用安装和使用typescript。

<!--more-->
# 优点
## 程序更容易理解
* 动态语言问题：
  * 函数或者方法输入输出的参数的类型不明确
  * 缺少类型约束，变量可以随意转换类型，需要不停的调试，查看变量的类型
* typescript 代码本身就能解释上述的问题

## 效率更高
* 在不同的代码块和定制中进行切换
* 能够与编辑器更好的交互：
  * 代码自动补全
  * 良好的 IDE 支持，可以提供更好的代码提示

## 更少的错误
* 编译期间能够发现大部分错误
* 杜绝一些比较常见的低级 Bug：
  * 变量名称打错
  * 传入错误类型的参数，例如传入 数字 0

## 非常好的兼容性
* 完全兼容 javascript
* 第三方库可以单独编写类型文件

# 缺点或者问题
* 学习成本有点高
* 短期内增加了一些开发的成本（长期减少了维护的成本）


# 安装

```bash
npm install typescript -g
```

# 使用
写代码
编写 hello.ts 文件
```typescript
const hello = (name: string) => {
  return `Hello world, I am ${name}`
}
console.log(hello('lvjiawen'))
```
执行一下编译命令，tsc 就是 ts compile 的意思，之后输入 ts 文件的名称，会自动生成相应的 js 文件，例如 hello.ts 会被编译成 hello.js
```bash
tsc hello.ts
// 编译结束之后
node hello.js
```

打开 hello.js，文件如下

```javascript
var hello = function (name) {
    return "Hello world, I am " + name;
};
console.log(hello('lvjiawen'));
```

下面的一节会继续介绍 typescript 的基本数据类型

