---
title: Node.js Http 模块
date: 
categories:
- Node
tags:
- Node.js
---

# http 模块

## res 属性

1. `res.writeHead(200,{"Content-Type":"text/html;charset=UTF8"})`
2. `res.write("<h1>我是一级标题</h1>")`
3. `res.end("<h2>我是二级标题</h2>")`
4. write 和 end 之中必须是字符串或者Buffer，别的内容需要`.toString()`才能输出
5. 所有的http请求必须要有一个res.end()，否则会一直挂起

## req 的属性

1. req.url 属性最重要，所有路由都必须最先拿到它
2. req.url 会忽略参数，可以识别？之后查询的参数，但是不能识别页面之内的#这样的
3. req.url 是以 “/” 开始的，表示根目录
4. 识别 URL 用到两个新模块：URL模块和 querystring 模块

### querysting

1. querystring.parse 接受一个查询字符串(?后面的东西)，返回一个对象，就是把 `'foo=bar&baz=qux&baz=quux&corge'`变成`{foo:'bar', bar:['qux', 'quux'], corge: ''}`
2. querystring.parse 和url.parse 中传入第二个参数true是一样的，所以现在推荐第二种写法，现在querystring现在基本没用了

### URL

1. 一个完整的请求路径：`http://user:pass@host.com:8080/p/a/t/h?query=string#hash` 可以划分为
   1. protocol：http
   2. slashes： protocol后面是否有斜线
   3. host: 主机名
   4. auth：用户
   5. hostname: 主机名，host.com
   6. port: 端口号，8080
   7. query: 网址的查询部分，可以通过第二个参数是true还是false决定返回的是字符串还是对象
   8. search: 根没传参数的query是一样的
   9. pathname: 路径，host之后和query之前的内容，包含最开始的那个斜线，例子中的 “/p/a/t/h”，
   10. path：pathname和search的合体，是“/p/a/t/h?query=string#hash”
   11. 
   12. hash：页面中的锚点
   13. 
2. `var url = require("url"); var path = url.parse(req.url).path`，parse可以把url转换为一个对象 
3. url.parse 的第二个属性填入true，url 对象中的 query 属性就会得到一个字符串

## 实战内容

1. 可以通过判断用户输入的url最后有没有 . 来判断请求的是文件还是文件夹
2. 当出现异步的时候，要么把它放在回调函数里面，要么给通用函数设计一个回调函数
3. Node.js 不好用的地方
   1. 实现一个静态的服务器需要写大量的代码，没有 web 容器
   2. 缓存问题，没有 304 ，用cookies打标记，查看last modified
   3. express 实现静态只需要一行代码


## 模块

1. exports 是 Node.js 穿件的一个对象，挂在上面的属性和方法，将完全付给`let a = require('xxx')` 中的 a 这个变量，a 接受 exports 变量，a 对象就是文件中的exports 对象，一般把变量设置成包的名字，相当于自动增加了一个**顶部命名空间**。
2. require 一次相当于增加了一个顶层变量，所有的函数和变量都要为这个顶层变量打点。
3. 前端代码的暴露，暴露的是个命名空间

```javascript
(function() {
    window.obj = {};

    obj.info = "";
})()
```

4. 可以用一个 Javascript 文件描述一个类，用 `module.exports = 构造函数名` 的方式暴露一个类，People 是一个构造函数，可以new出新对象

```javascript
function People(name, sex, age) {
    this.name = name;
    this.sex = sex;
    this.age = age;
}

People.prototype = {
    sayHello : function() {
        console.lo(this.name + this.sex + this.age)
    }
}

module.exports = People;
```

5. 综上，有两种js文件写作的方式，一种方式暴露一个类(module.exports = 类名)，第二种是暴露属性和方法（exports.msg = msg）
6. 不写 ./ 的时候，默认从 node_modules 文件夹引入，node 会逐级遍历指导找到node_modules 文件夹
7. package.json 中 main 表示入口文件，每一个模块文件夹中推荐写一个package.json文件，Node 会自动读取它。
8. 模块就是文件盒文件之间的关系

## POST 请求

1. post 请求是怎么玩的

```javascript
var postData = "";

req.addListener('data', function(chunk) {
    postData += chunk;
})

req.addListener('end', function() {
    var param = querystring.parse(postData);
    res.writeHead(200, {"Content-Type": "text/html; charset=utf8"});
    res.end(param.name);
})
```

## ejs 模板

1. 时代的潮流是，用户访问首页的时候产生静态文件，前端用 ajax 请求数据 json 文件
2. underscore 这样的前台模板，请求一个json，后台服务器只输出json，不输出样式
3. angular.js 这些就是优雅的把后台的数据变成前端的页面
4. ejs 这样的技术，技术要杂糅，前后端分的不够开
5. ejs 这个模板引擎的效率并不高，因为它实际上是后台在操作字符串

## jade 模板

1. 靠的是字符缩进
2. 效率高，解析快