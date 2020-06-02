---
title: ES 5 / 6 / 7 / 8 新特性
date: 
categories:
- 编程语言
tags:
- 编程语言
- ES
---

## ES 5 / 6 / 7 / 8 新特性

### 箭头函数

bind ，每次会返回一个新的函数的引用，想引用同一个函数的时候要注意保留一下引用。

> 错误的做法

```
class PauseMenu extends React.Component {
	componentWillMount() {
		AppStateIOS.addEventListener('change', this.onAppPaused.bind(this))
	}
	componentWillUnmount() {
		AppStateIOS.removeEventListener('change', this.onAppPaused.bind(this))
	}
	onAppPaused(event) {}
}
```

> 正确的做法

```
class PauseMenu extends React.Component {
	constructor(props) {
		super(props)
		this._onAppPaused = this.onAppPaused.bind(this)
	}
	componentWillMount() {
		AppStateIOS.addEventListener('change', this._onAppPaused)
	}
	componentWillUnmount() {
		AppStateIOS.removeEventListener('change', this._onAppPaused)
	}
	onAppPaused(event) {}
}
```

还可以

```
class PauseMenu extends React.Component {
	componentWillMount() {
		AppStateIOS.addEventListener('change', this.onAppPaused
	}
	componentWillUnmount() {
		AppStateIOS.removeEventListener('change', this.onAppPaused
	}
	onAppPaused = (event) => {}
}
```

### 解构赋值

#### 获取数组中的值

从数组中获取值，并赋值到变量中，变量的顺序与数组中对象顺序对应

```
var foo = [1, 2, 3, 4]
var [one, two, three] = foo
console.log(one, two, three)

var [first, , , last] = foo
console.log(first, last)

var [a, b] = [9, 10]
console.log(a, b)
```

如果没有从数组中取到值，可以为变量设置一个默认值

```
var a, b

[a=5, b=7] =[1]
console.log(a, b)
```

通过解构赋值还可以方便的交换两个变量的值

```
var a = 1,b = 3;
[a,b] = [b, a]
console.log(a, b)
```

#### 获取对象中的值

```
const student = {
	name: 'Lvjiawen',
	age: 18,
	city: 'Hangzhou'
}
const {name, age, city} = student
console.log(name, age, city)
```

### 延展运算符

延展运算符 ... 可以在函数调用或者数组构造时，将数组表达式或者 string 在语法层面展开；还可以在构造对象时，将表达式按 key-value 的方式展开

> 函数调用

```js
var iterableObj = ['my', 'name', 'is', 'lvjiawe']
function myFunction() {
	console.log(arguments) // ['my', 'name', 'is', 'lvjiawe']
}
myFunction(...iterableObj)

function sum(x, y, z) {
  return x + y + z
}
var numbers = [1, 2, 3]
console.log(sum.apply(null, numbers)) // 6
console.log(sum(...numbers)) // 6
```

> 数组构造或字符串

没有展开语法的时候，只能组合使用 push，splice，concat 等方法，来将已有数组元素变成新的数组的一部分，有了展开语法，构造新数组变得更简单

```js
var iterableObj = ['my', 'name', 'is', 'lvjiawen']
var arr = [...iterableObj, '4', ...'hello',6]
console.log(arr) // ["my", "name", "is", "lvjiawen", "4", "h", "e", "l", "l", "o", 6]

var arr1 = [1, 2, 3]
var arr2 = [4, 5, 6]
var arr3 = [...arr1, ...arr2] // 等同于 var arr3 = arr1.concat(arr2)
```

> 构造对象时，进行克隆或者属性拷贝

```js
let obj =  {name: 'Lvjiawen', age: 18, like: {film: 'one world'}}
var obj2 = {hello: 'world'}
let objClone = {...obj}
var objMerge = {...obj, ...obj2}
obj.name = 'changed';obj.age = 'changed';obj.like = 'changed'
console.log(obj, objClone, objMerge)

let arr = [1, 2, 3]
let arr2 = [...arr] // 相当于 arr.slice()
arr2.push(4)
console.log(arr, arr2)	// [1, 2, 3] ,  [1, 2, 3, 4]
```

> React 中的应用

```
var params = {param1: 123, param2: 456, param3: 789}
var {type, ...other} = params
<Comp type='normal' {...other}></Comp> // 相当于<Comp type='normal' param2={456} param3={789}></Comp>
```



### Promise

```
var waitSecond = new Promise(function(resolve, reject) {
	setTimeout(resolve, 1000)
})
waitSecond.then(_ => {console.log('1 second passed')})
```

### let 和 const

```
{var a = 10};console.log(a); // 10
{let b = 100};console.log(b); // ReferenceError
```

### ES7

#### Array.prototype.includes() 

返回 true 或者 false

```
arr.includes(0) // 等价于 arr.indexOf(0) >=0
```

#### 指数操作符

在 ES7中引入了 指数运算符 \*\*，\*\* 具有与 Math.pow() 一样的计算结果



### ES8

#### async / await

不使用 async await

```
function login(userName) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {resolve('1001')}, 600)
	})
}

function getData(userId) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
      if (userId === '1001') {
        resolve('登陆成功')
      } else {
      	reject()
      }
		}, 600)
	})
}

function doLogin() {
	login('lvjiawen')
		.then(getData)
		.then(result => {
			console.log(result)
		})
		.catch(() => {
			console.log(登录失败)
		})
}

async function doLogin2() {
	const userId = await login('lvjiawen')
	const result = await getData(userId)
	console.log(result)
}

doLogin()
doLogin2()
```

> 获取异步函数的返回值

```
function charCount(data) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
    	console.log(2000)
			resolve(data.length)
		}, 2000)
	})
}

async function charCountAdd(data1, data2) {
	const d1 = await charCount(data1).catch(e => console.log('d1 is wrong'))
	const d2 = await charCount(data2).catch(e => console.log('d2 is wrong'))
	return d1 + d2
}

const result = await charCountAdd(null, 'world').then(res=> console.log(res)).catch(err => console.log('报错'))
```

> async / await 在并发场景中的应用

```
function charCount(data) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
    	console.log(5000)
			resolve(data.length)
		}, 5000)
	})
}

async function charCountAdd(data1, data2) {
	const [d1, d2] = await Promise.all([charCount(data1), charCount(data2)])
	return d1 + d2
}

charCountAdd('hello', 'world').then(res=> console.log(res)).catch(err => console.log('报错'))
```

#### Object.values()

与 Ojbect.keys() 类似，返回自身属性的所有值，不包括继承的值

```
var obj = {a: 1, b: 2, c: 3}
console.log(Object.keys(obj)) // ["a", "b", "c"]
console.log(Object.values(obj)) // [1, 2, 3]
```



#### Object.entries()

#### String padding

#### 函数参数列表结尾允许逗号

#### Object.getOwnPropertyDescriptors()

