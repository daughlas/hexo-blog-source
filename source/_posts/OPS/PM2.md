---
title: PM2
date: 
categories:
- 运维
tags:
- 运维
---


## PM2
pm2（process manager）是一个进程管理工具，维护一个进程列表，可以用它来管理你的node进程，负责所有正在运行的进程，并查看node进程的状态，也支持性能监控，负载均衡等功能。

* 进程守护，系统崩溃自动重启
* 启动多进程，充分利用 CPU 和内存
* 自带日志记录功能

### PM2 介绍

#### 下载安装

`npm install pm2 -g`

`pm2 --version`

#### 基本使用

启动 `cross-env NODE_ENV=production pm2 start app.js`

查看 `pm2 list`

#### 核心价值

* 进程守护
* 多进程启动
* 线上日志记录

#### 常用命令

`pm2 start …` 可以跟配置文件作为参数

`pm2 list` 程序列表

`pm2 restart ${appName|/${id}`  重启进程

`pm2 stop ${appName|/${id} `停止

`pm2 delete ${appName|/${id}` 删除

`pm2 info ${appName|/${id} ` 信息

`pm2 log ${appName|/${id} ` 日志

`pm2 monit ${appName|/${id}` 监控

### PM2 进程守护

pm2 遇到进程崩溃，会自动重启

### PM2 配置和日志记录

#### 配置

* 新建 PM2 配置文件（包括进程数量，日志文件目录等）

pm2.config.json

```javascript
{
  "name": "pm2-test-server",
  "script": "app.js",
  "watch": true, // 是否监听文件变化
  "ignore_watch": [
    "node_modules",
    "logs"
  ],
  "error_file": "logs/err.log",
  "out_file": "logs/out.log",
  "log_date_format": "YYYY-MM-DD HH:mm:ss"
}
```



* 修改 PM2 启动命令，重启
* 访问 server，检查日志文件的内容（日志记录是否生效）

### PM2 多进程

#### 为何使用多进程

![image-20190628101837144](/images/image-20190628101837144.png)

* 单个进程的内存是受到限制的
* 一个进程无法利用机器的全部内存
* 多进程能够充分利用 CPU 多核的优势

#### 多进程和 redis

多个进程的 session 无法共享

![image-20190628102048890](/images/image-20190628102048890.png)

使用redis 解决

![image-20190628102123289](/images/image-20190628102123289.png)

pm2 会自动在寻找空闲进程进行负载均衡



