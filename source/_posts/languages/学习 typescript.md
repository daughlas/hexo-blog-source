---
title: 学习 TypeScript
date: 
categories:
- 编程语言
tags:
- 编程语言
- TypeScript
---

## 类

有两种相同的写法
写法一：

```
class Person {
    name;

    constructor(name: string) {
        this.name = name
    }
}
```

写法二： 

```
// 构造函数里面要明确书写访问控制符，否则会报错
class Person {
    constructor(public name:string) {

    }
}
```

typeScript 类的继承，extends 和 super，extends的类会获得所有的属性和方法
super 调父类的构造函数



```
var node = document.createTextNode('');
```

# JSONP

1. json with padding
2. 利用了script 标签的特性
   1. 可以加载在线资源
   2. 没有跨域的限制
   3. script 能不能加载 jsp、php 文件呢，只要是合法的 js 就行
   4. 拿过来之后要解决，数据没有名字，用不了。
3. 计算机区分文件类型不是根据文件的后缀名，后缀名是给人看的，给第三方软件进行关联的。
   例如你在一个 1.txt 的文件中写 `var a = 0;` 用 script 引入之后，后面写 `alert(a);`，是可以打印出 100 的，这个时候你把文件名换成 png 还是可以打印出100。

# typeScript

1. es6 模板字符串
   1. 其中 ` 用来定义 ES6 中的模板字符串
   2. ${expr}
2. 在 TypeScirpt 中，可以用 void 表示没有任何返回值的函数

```ts
function alertName(): void {
  alert('My name is xcatliu');
}
```

1. 任意值（Any）用来表示允许赋值为任意类型。
   1. 在任意值上访问任何属性都是允许的。
   2. 在任意值上可以调用任何方法。
   3. ？声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值。
   4. 未声明类型的变量，那么它会被识别为任意值类型。
2. 联合类型，类型可以是特定几种中的一种 `let myFavoriteNumber: string | number;`
   注意，这样的定义只能访问到它们共有的类型和方法
3. 在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implements）。

```ts
interface Person {
  name: string;
  age: number;
}

let xcatliu: Person = {
  name: 'Xcat Liu',
  age: 25
};
```

上面的例子中，我们定义了一个接口 Person，接着定义了一个变量 xcatliu，它的类型是 Person。这样，我们就约束了 xcatliu 的形状必须和接口 Person 一致。定义的变量比接口少了一些属性是不允许的：属性多了也不行，反正是要一样。如果 `age: 25` 改成 `age?: 25` 就可以让形状不完全一致了。可以不存在，但是还是不允许添加不存在的属性。

有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 readonly 定义只读属性：

```ts
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
};

let xcatliu: Person = {
    id: 89757,
    name: 'Xcat Liu',
    website: 'http://xcatliu.com'
};

xcatliu.id = 9527;
```

1. 数组可以用「类型 + 方括号」表示法，例如 `let fibonacci:number[] = [1,2,4,5,6]` 、 `let fibonacci:(number|string)[] = [1,2,4,5,6]`，数组中不允许出现其它类型。
2. 也可以使用数组泛型（Generic） Array<elemType> 来表示数组： `let fibonacci: Array<number> = [1, 1, 2, 3, 5];`
3. ts 定义函数的两种方式，定义了两个参数，调用的时候也必须传递连个参数，不能多，也不能少。

在 TypeScript 的类型定义中，=> 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。

```ts
// 函数声明
function sum(x: number, y: number): number {
  return x + y;
}

// 函数表达式
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
  return x + y;
};
```

用？ 的可选参数，调用函数的时候可以不传，可选参数必须接在必需参数后面。

```ts
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + ' ' + lastName;
  } else {
    return firstName;
  }
}
let xcatliu = buildName('Xcat', 'Liu');
let xcat = buildName(undefined, 'Xcat');
```

1. 在 ES6 中，可以给函数的参数添加默认值， TypeScript 会将添加了默认值的参数识别为可选参数

这个时候，不再必须让可选参数必须放在必需参数后面

```ts
function buildName(firstName: string, lastName: string = 'Liu') {
    return firstName + '' + lastName;
}

let scatliu = buildName('Xcat', 'Liu');
let xcat = buildName('Xcat');
```

1. ES6 中，可以使用 ...rest 的方式获取函数中的剩余参数（rest 参数）：

```ts
function push(array, ...items) {
  items.forEach(function(item) {
    array.push(item);
  });
}

let a = [];
push(a, 1, 2, 3);
```

事实上，items 是一个数组。所以我们可以用数组的类型来定义它：

```ts
function push(array: any[], ...items: any[]) {
  items.forEach(function(item) {
    array.push(item);
  });
}

let a = [];
push(a, 1, 2, 3);
```

1. 重载

重载允许一个函数接受不同数量或类型的参数时，做出不同的处理。

1. 类型断言:在需要断言的变量前加上 <Type> 即可,

```ts
function getLength(something: string | number): number {
    if (<string>something).length {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
```

1. 内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准。

```ts
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

