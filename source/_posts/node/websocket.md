---
title: socket
date: 
categories:
- Node
tags:
- Node.js
---

## http

1. http协议是无状态的，服务器只会响应来自客户端的请求，但是它与客户端之间不具备持续的连接
2. 我们可以轻松捕获浏览器的事件，比如用户点击盒子，这个时间可以轻松产生与服务器的数据交互，比如发送 Ajax 请求。但是，反过来却是不可能的，服务器发生了一个事件，服务器无法将这个事件的信息主动通知它的客户端，只有在用户端查询服务器的当前状态的时候，所发生的事件信息才会从服务器传递到客户端。
3. 回调函数什么的都是因为客户端发生各种请求，才有回调的。http 协议就是监听各种请求。
4. http 难以实现实时应用。
5. 解决办法：
   1. 长轮询：每间隔1s查询一次服务器，最早的时候都是这种模式
   2. 长连接：客户端只请求一次，服务器连接保持，不会返回结果，不写response.end()
   3. 问题：性能浪费
   4. h5 解决办法，退出了一个WebSocket协议，能够让浏览器和服务器进行全双工的实时通讯，服务器也能主动通知客户端了。

## WebSocket

1. 为解决实时通讯问题，h5 推出了一个 WebSocket 协议，能够让浏览器和服务器进行全双工的实时通讯，服务器也能主动通知客户端了。
2. 原理，利用 http 请求产生握手，http头部中含有 WebSocket 协议的信息，握手之后，二者转用 TCP 协议进行交流（QQ的协议），就变成了一个有状态的实时连接。
3. WebSocket 需要浏览器得支持(chrome4, firefox4, ie10, safari5)，更需要服务器支持(Node 0, Apach 7..0.2, Nginx 1.3)
4. Node.js 上实现需要很多代码，Socket.IO 成为事实上的标准，新手福音，业界良心。它屏蔽了所有底层细节，让顶层调用非常简单，并且还为不支持 WebSocket 协议的浏览器，提供了长轮询的透明模拟机制。
5. Node的单线程、非阻塞I/O、事件驱动机制，使它非常适合当socket 服务器。
6. socket 是插座的意思，象征着保持着通路，专业术语叫做套接字
7. socket 的使用，输入之后http://localhost:3000/socket.io/socket.io.js 这个网址就会显示为一个 js 文件，此时需要配置html文档，创建一个页面，引入这个 js 文件 `<script src="/socket.io/socket.io.js"></script>` ，这是一个绝对地址，因为第一个 / 表示根路径。还要写一条语句 `<script>var socket = io()</script>`

```javascript
const http = require('http');

let server = http.createServer((req, res) => {
    res.end('hello world')
});

let io = require('socket.io')(server);

io.on('connection', (socket) => {
    console.log('一个客户端连接了')
})

server.listen(3000, () => {
    console.log('Listening Port: 3000');
})

```

8. 进行了上面的操作之后，服务器和客户端就都有 socket 对象了，彼此都有 emit 方法 和 on方法，写法
   `socket.on(事件名，(msg) => {})` 和 `socket.emit(事件名，msg)`
9. 不仅可以发字符串，还可以发字符串，数组，两方面同时都是 js 环境，node.js 非常适合做socket。
10. io.emit 广播，所有连接进来的人都会收到通知，默认是点对点，每一个连接进来的人都会创建一个专属于他的socket
11. Express 和 socket.io 能特别方便的配合，固定的写法如下

```javascript
var express = require('express');
var app = express();
var http = require('http').Server(app);
var io = require('socket.io')(http)
http.listen(3000);
```





