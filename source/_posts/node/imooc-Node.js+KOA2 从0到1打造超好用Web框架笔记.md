---
title: imooc-Node.js+KOA2 从0到1打造超好用Web框架笔记
date: 
categories:
- Node
tags:
- Node.js
- imooc 实战
- 待续
---


# 第 1 章

## 1-1 导学

### Node.js 能力

* 脱离浏览器运行 JS
* Node.js Stream （前端工程化基础）
* 服务端 API
* 作为中间层

### CTO 往往是服务端工程师

* 需要设计整个公司技术架构
* 需要从全局考虑问题
* 需要掌控公司最重要的资产：数据（谁掌握数据，谁才有话语权）

## 1-2 异步、javascript 特性与 Node.js

> 编程，实践性非常强，注意，不要把关注点仅仅放在业务上，业务只是为了承载知识。

# 第 2 章 深入理解 KOA

## 2-3 KOA 的精简特性与二次开发必要性分析

* 后端不同于前端：读写数据库、API
* 框架：
  * 写出好的代码
  * 提高开发效率
* 数据库
  * 悲观锁
  * 乐观锁
  * 事物
  * 脏读
  * 幻读
* Node.js API 低级，基础

* KOA 特点
  * 洋葱圈模型
  * 精简 => 定制化能力强，适合二次开发



## 2-4 模块加载、ES、TS、Babel 浅析

### 模块

#### commonJS

```
const Koa = require('koa')
```

#### ES6

```
import Koa from 'koa'
```

### Node 里边不能用的 ES 新特性

* import 不能使用
* decorator不能使用
* class 的时候必须要写 this.x



## 2-5 KOA 中间件

中间件，就是函数

```js
const koa = require('koa')
const app = new koa()

app.use((ctx, next) => {
  console.log(1)
  next()
})
app.use(() => {
  console.log(2)
})

app.listen(3000)
```

### 2-6 洋葱圈模型

```js
const koa = require('koa')

const app = new koa()

// 应用程序对象，挂载很多中间件
// 注册
app.use((ctx, next) => {
  console.log(1)
  next()
  console.log(2)
})
app.use((ctx, next) => {
  console.log(3)
  next()
  console.log(4)
})

// 打印顺序 1，3， 4， 2
app.listen(3000)
```

注意：

* **use 的回调函数前边一定要写 async**
* **next 前边一定要写await**

### 2-7 强制 Promise

```
const koa = require('koa')
const app = new koa()

app.use((ctx, next) => {
  console.log(1)
  const a = next()
  console.log(a) // Promise { 'abc' }
  console.log(2)
})
app.use((ctx, next) => {
  console.log(3)
  console.log(4)
  return 'abc'
})

app.listen(3000)
```

### 深入理解 async 和 await

* await 可以理解为求值关键字，将 Promise 转化为成功时的返回值，await 可以对任何表达式求值
* await 会阻塞线程
* async 的意义就是加上 async 的函数，任何返回值都会被包装成 Promise

```js
const koa = require('koa')
const app = new koa()

app.use(async (ctx, next) => {
  console.log(1)
  const a = await next()
  console.log(a) // abc
  console.log(2)
})
app.use((ctx, next) => {
  console.log(3)
  console.log(4)
  return 'abc'
})

app.listen(3000)
```



### 2-9 为什么一定要保证洋葱圈模型

如果不在 next 前加 await，不在中间件函数里边加 async，就不能保证洋葱圈模型了

```js
const koa = require('koa')
const app = new koa()

app.use(async (ctx, next) => {
  console.log(1)
  next()
  console.log(2)
})
app.use(async (ctx, next) => {
  console.log(3)
  const axios = require('axios')
  await axios.get('https://www.baidu.com')
  next()
  console.log(4)
})

// 1, 3, 4, 2, 'abc'
app.listen(3000)
```

axios 请求的时候阻塞了线程，遇到阻塞线程的操作的时候，js 就回去执行下面的代码

中间件函数被next 分为两部分，next 函数之前的部分，所有之后的中间件都还没有执行，next 之后的部分，所有中间件都已经执行完毕了。



## 第3章

### 3-1 路由系统

啥啥没有的情况

```js
const koa = require('koa')
const app = new koa()
app.use(async (ctx, next) => {
  let {path,method} = ctx.request
  if (path === '/classic/latest' && method === 'GET') {
    ctx.body = {
      key: 'classic'
    }
  }
})
app.listen(3000)
```

简单使用 koa-router

```js
const koa = require('koa')
const Router = require('koa-router')

const app = new koa()
let router = new Router()

router.get('/classic/latest', async (ctx, next) => {
  ctx.body = {
    key: 'classic123123'
  }
})
app.use(router.routes()).use(router.allowedMethods())
app.listen(3000)
```

### 3-2 服务端编程必备思维：主题与模型划分

* 好的代码利于阅读
* 好的代码利于维护
* 好的代码利于提高编程效率

主题：抽象的概念，抽象的概念有助于思考，概念类似于 Model

主题的划分：

* 期刊
* 书籍
* 点赞
* 用户

### 3-3 多 router 拆分路由

#### API 携带版本号：

1. 放在路径之中
2. 放在查询参数
3. 放在 http header 之中

#### 开闭原则

* 对修改关闭，对拓展开放
* 对每个版本的 API 都写一个路由函数





# 第4章

## 4-1 参数获取

* path 路由参数：`ctx.params`
* query 参数: `ctx.request.query`
* headers 参数: `ctx.request.headers`
* body 参数: `ctx.request.body`（需要使用body-parser： `app.use(parser())`）

## 4-2 异常理论与异常链

### 异常链

```
function func1() {
  try {
    func2()
  } catch (error) {
    throw error
  }
}

function func2() {
  try {
    func3()
  } catch (error) {
    throw error
  }
}

function func3() {
  try {
    console.log(0/a)
  } catch (error) {
    throw error
  }
}

func1()
```

## 4-3 异步异常处理

try-catch 只对同步的方法有效，不能捕获异步的异常

> 返回 Promise 的函数就可以使用 async 和 await 来改写

await 对异步操作求值

await 把异步的异常给释放了出来

很多后如果不加 await ，异常就会变成一个 unhandled promise rejections

```js
async function func1() {
  try {
    await func2()
  } catch (error) {
    throw error
  }
}

async function func2() {
  try {
    await func3()
  } catch (error) {
    // throw error
    console.log('error')
  }
}

function func3() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const r = Math.random()
      if (r < 0.5) {
        reject('error')
      } else {
        resolve('hello')
      }
    }, 1000)
  })
}

func1()
```

## 4-4 全局异常处理中间件编写

面向切面编程 AOP，不考虑细节

```js
const catchError = async function(ctx, next) {
  try {
    await next()
  } catch (error) {
    ctx.body = '服务器有点问题，你等一下'
  }
}

module.exports = catchError
```

### 4-5 已知错误与未知错误

应该返回给前端：

* HTTP status code： 2xx， 4xx， 5xx
* message 具体的，文字化的信息
* error_code 
  * 开发者自己定义
  * 可以是五位 10001， 20003
* request_url



错误的分类

* 已知型错误，参数类型不符合这些
* 未知型错误，程序潜在的错误，开发者无意识的

## 4-6 定义异常返回格式

抛出异常

```js
const error = new Error('我是错误信息')
error.errorCode = 10001
error.status = 400 // 参数错误
error.requestUrl = `${ctx.method} ${ctx.path}`
throw error
```

处理错误中间件：

```js
const catchError = async function(ctx, next) {
  try {
    await next()
  } catch (error) {
    if (error.errorCode) {
      // 已知异常
      ctx.body = {
        msg: error.message,
        errorCode: error.errorCode,
        request: error.requestUrl,
      }
      ctx.status = error.status
    }
  }
}

module.exports = catchError
```

### 4-7 定义 HttpExeption 异常基类

```
class HttpException extends Error {
  constructor(msg='服务器异常', errorCode=10000, code=400) {
    super()
    this.errorCode = errorCode
    this.code = code // httpStatus
    this.msg = msg
  }
}

module.exports = HttpException
```

### 4-8 特定异常类与 global 全局变量



# 第 5 章

 

## 5-2

lodash 一个库

深拷贝

validator.js 一个库



## 5-4

通用型用户系统

* 账号、密码、附属信息
* 注册、登录



数据库：

* 非关系型：
  * （文档型）：MongoDB（持久化数据）
  * key-value 型：Redis（缓存、提高查询数据的速度）
* 关系型：
  * My SQL


### 5-6 Sequelize 初始化配置与注意事项

Wequelize 链接数据库，配置数据库参数



主键，不能重复，不能为空，可以自动增长



数字的主键查询性能好

1，2，3 这种自增长的字符串，遇到 1000 个并发很有可能就会计算重复

自增长 会 暴露用户编号，但是其实没什么，要做到，即使知道用户编号，也做不了什么

接口保护 鉴定权限 访问接口 Token

上线之后，数据迁移或者手动维护来更改表，推荐手动维护

数据迁移，SQL 更新，表设计的不好的时候可以用一下



设计接口的思维路径：（注册为例）

* 先考虑接受的参数
  * email
  * password1
  * password2
  * nickname
* 在引入 Validator





中间件是一个静态的方式，每一个请求进来都会实例化 RegisterValidator，10个请求10个实例，下面这样写

```
router.post('register', new RegisterValidator(), async (ctx, next) => {
	// ...
})
```

只在 KOA 项目启动的时候，new 一次 RegisterValidator，问题时 挂载在 RegisterValidator 实例上的属性就会被许多请求更改



函数通常不会保存变量的状态，类实例化的对象可以保存变量的状态