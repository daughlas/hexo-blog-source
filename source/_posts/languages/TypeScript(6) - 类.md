---
title:  TypeScript(6) - 类
date: 2019-09-23 12:11:38
categories: [编程语言]
tags: [编程语言, TypeScript]
---

> 摘要：简单介绍 typeScript 中的 Class

<!--more-->
之前 javascript 中实现面向对象，需要使用构造函数和原型链。ES6提供了新的 class 语法支持，虽然底层实现还是原型，但是有了 class 可以更方便用与其他语言相似的思维来思考代码。

类：定义了一切事物的 抽象 特点，其中包含属性和方法，就像冯诺伊曼体系结构。
实例：用 new 构造出来的实例， MacbookPro 2018 late 可以看作是一个实例
面向对象：抽象、封装、继承、多态

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }

  run() {
    return `${this.name} is running`;
  }
}

const cat = new Animal('shimei');
console.log(cat.run());
```

简单的继承一下，然后增加一个 bark 方法

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }

  run() {
    return `${this.name} is running`;
  }
}



class Dog extends Animal {
  bark() {
    return `${this.name} is barking`;
  }
}


const cat = new Animal('shimei');
console.log(cat.run());
const dog = new Dog('xiaohei');
dog.bark();
```

继承的同时，如果需要重写父类的方法
```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }

  run() {
    return `${this.name} is running`;
  }
}



class Dog extends Animal {
  bark() {
    return `${this.name} is barking`;
  }
}

class Cat extends Animal {
  constructor(name) {
    super(name + 'Cat');
  }

  run () {
    return 'Cat ' + super.run();
  }
}


const cat = new Animal('shimei');
console.log(cat.run());
const dog = new Dog('xiaohei');
console.log(dog.bark());
const cat2=new Cat('shimei2')
console.log(cat2.run());
```

访问修饰符：
* public 所有地方都可以访问，默认就是 public 的，比如 name，外界可以通过 `xxx.name` 的方式来修改属性
* private，实例对象不能访问，其子类也不能访问
* protected，实例对象不能访问，子类可以访问
  * readonly 实例对象不能访问，子类可以读，不能重新赋值

静态属性，静态方法，属于类的属性和方法，跟实例没什么关系

```typescript
class Animal {
  private name: string;

  static categories: string[] = ['mammal', 'bird']

  static isAnimal(instance) {
    return instance instanceof Animal
  }

  constructor(name: string) {
    this.name = name;
  }

  run() {
    return `${this.name} is running`;
  }
}
```