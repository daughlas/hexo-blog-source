---
title: 学习 Express 框架
date: 
categories:
- Node
tags:
- Node.js
---

## express 整体感知

1. 出色的路由能力，正则，“：”方法等
2. 静态文件处理，用 `app.use(express.static('./public')'`就行了
3. 与模板引擎配合

## static

1. express.static 实现静态页面服务
2. express.static 和路由冲突的时候，看谁在上面，就会把路由拦截下来

## 路由

1. app.get(网址, 回调)，当用get 方法访问网址的时候进行什么样的处理
2. app.post(网址, 回调)，当用post 方法访问网址的时候，进行什么样的处理
3. app.all(网址, 回调)，当用任何方法访问这个网址，都用同样的处理
4. 互联网的网址是不区分大小写的，这个是 http 协议规定的
5. 路由到 /a ，实际 /a?id=2&se=man#hello，也是被这个网址的回调函数处理的
6. app.get(/\/commits\/(/w+)(?:\.\.(\w+)?$/, function)，可以在地址中使用正则表达式，通过req.params[0] 得到圆括号分组的结果，实际上express上用：的方式更好
7. 推荐冒号的写法 `app.get('/user/:id',function(req, res) {})` ，可以通过req.params["id"] 得到
8. 表单可以自己提交到自己上，一个 get 请求，一个 post 方法，可以节约路由器
9. RESTful 路由设计
   1. /student/:id
      1. get 获取学生信息
      2. add 添加学生
      3. delete 删除学生信息
   2. 一个路径，根据 http 的方法不同，对这个页面的使用不同
   3. express 特别适合进行这样的路由设计
10. 网页基本上只能处理 get 和 post 请求，软件和 app 可以发起各种不同种类的请求

## 中间件

1. 中间件的**顺序**很重要，越详细的越往前写，越抽象的越往后写
2. express 寻找路由的顺序，是按照程序书写时从上到下，上面找到了就执行，下面符合的地址就执行不了了，匹配第一个路由之后不会继续匹配了。
3. 想要继续寻找路由，需要在回调中添加一个 next 参数`(req, res, next)`，并执行它 `next()`，这样可以用两段小程序处理一个事，可以把业务分开。
4. 解决路由冲突的第一种方法合理设置顺序，匹配了第一个，就不会往下匹配了。路由表，具体的往上写，抽象的往下写。
5. 第二种解决方法是加入判断和 next()，还有两项业务挂载到一个路由上，注意第一个send之后，不能进行第二次send，next 的时候最后要一个中间件接受不满足所有匹配，最后会进行怎样的操作。
6. express 中有个很抽象的 app.use([path,]function[,function]) 函数，可以填写多个地址，用了 use 之后，设定了 path 之后，客户端访问path/任意东西，都会执行这个回调函数，可以把第一个参数的层级无限扩充下去，提供了req.originUrl 填写的全部网址，baseUrl app.use 中写的网址，req.path 第一个减去第二个
7. app.use('/',function) ，任何网址都会经过这个路由，但是会截断路由，这个必须得写next，不写网址的时候，也相当于写了一个 '/'，也就是所有网址
8. app.use 的主要作用就是后面传入其他的参数和函数，这个回调会默认被传入 req 和 res 两个参数
9. `app.use('/static', app.static('./public'))` 可以把静态文件挂载到 static 这个路由下面，这样路由和文件夹的名字就不怕冲突了
10. 用 express 写 404，最后放一个，`app.use(function(req, res) {res.send("没有这个页面")}`，所有路由都找不到的时候，就走这个路由，直接就是 404 了
11. post 必须用 body-parser 这个中间件
12. `app.get("/", showIndex)` 这个 showIndex 的函数不用穿 req 和 res，因为它是个中间件，相当于回调的时候自动送进去了两个参数

## 内容渲染

1. 大多数情况下，内容渲染用 res.render()，将会根据views 中的模板文件进行渲染，自己写文件夹 `app.set("views",文件夹名)`
2. 这个函数根据内容，自动帮我们设置了 Content-type 头部和 200 状态码。
3. 想写不同的状态码，可以用 `res.status(404).send("sorry, we can't find that!")`
4. 设置不同的 Content-Type，用 `res.set("Content-Type","text/html")`
5. res.send 和 res.end 一样，只能设一次，但是 send 有个好处，自动帮我们设置状态吗和 MIME 类型。

## post 和 get 请求

1. `app.use(express.static("./public")` 就解决了所有静态文件的问题
2. get 请求的参数在 url 中，原生 node 中需要 URL 模块来识别参数字符串。在 express 中，可以直接使用 req.query 对象
3. POST 在 express 中不能直接获得，body-parser 模块，使用之后可以用 req.body 得到参数。但是表单中含有文件的上传，那么需要使用 formidable 这样的工具
4. body-parser 的使用
   1. `const bodyParser = require("body-parser")`
   2. `app.use(bodyParser, urlencoded({extended: false}))`
   3. 或者 `app.use(bodyParser, json())`



## 其他

1. AJAX 请求的都是 JSON

2. node.js 所有的js文件可以从自己出发，用相对目录引用

3. Node.js 的编程思维：因为所有的东西都是异步的，所以，内层函数，不是return 回来东西，而是调用高层函数提供的回调函数。先干具体的事情，用回调代替返回值

   不是这样

   ```javascript
   res.render('index', {
       "name": student.getDetailById(234234).name
   })
   ```

   而是这样

   ```javascript
   student.getDetailById(234234, function(detail) {
       res.render('index', {
           "name": detail.name
       })
   })
   ```

4. Node 全是回调函数，如果我们自己的方法中包含IO或者数据库，也要用回调函数的方法封装。callback(err, null) 和 callback(null, err)

5. 可以将最后的app.use 写404