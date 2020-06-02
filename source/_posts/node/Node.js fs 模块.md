---
title: Node.js fs 模块
date: 
categories:
- Node
tags:
- Node.js
---

## 相关模块

1. fs
2. path
3. readline
4. fs-extra(第三方)

## path 模块

1. **文件操作过程中，都必须使用物理路径(绝对路径)**

2. path 提供了一系列与路径相关的API

3. `path.basename(p[, ext])` 获取文件名，第二个参数表示不带拓展名

4. `path.delimiter` 获取不同系统默认的分隔符，环境变量的分隔符，windows是';'，linux 是 ':'，举个应用的例子`console.log(process.env.PATH.split(path.delimiter))`

5. `path.dirname(p)` 获取论经中的目录名称

6. `path.extname(p)` 获取路径中的拓展名，包含'.'

7. `path.parse(pathString)` 将一个路径字符串转换成一个对象，包含目录，文件名，拓展名， 它的逆过程是 `path.format(pathObj)`

8. `path.isAbosolute(path)` 判断路径是否为绝对路径，设计 api 的时候判断一下别人传的是不是绝对路径，是的话就直接用，不是的话自己转换一下。

9. `path.join([path1][, path2][, path3])` 拼接路径，例如`path.join(__dirname, '..', './temp', 'a', '../../x.md` 相当于 ../x.md 所指向的绝对路径

10. `path.normalize(p)` 常规化一个路径，纠正写的不太对的路径

11. `path.relative(绝对路径a, 绝对路径b)` 获取b路径相对于a路径的相对路径。

12. `path.resolve(一对路径)` 生成一个应该指向的绝对路径，跟join 差不多

13. `path.sep` 当前操作系统中用的默认路径成员分隔符。windows是'\'，Linux 用的是 '/'

14. `path.win32` win32 指的是win32，在任何操作系统中使用windows 的方式操作路径，'path.posix' 允许在任何操作系统上使用 linux 的方式操作路径，使用 path 自动判断系统

15. **注意：赋值语句先执行右边的部分，所以不能循环定义**

    ```javascript
        var p = {
            xp : null;
        }
        p.xp = p;
        console.log(p == p.xp)
    ```

## 同步调用和异步调用

1. node 中所有的阻塞操作都提供了两种类型的 api，一种同步调用，一种异步调用，例如readFile() 和 readFileSync()

2. 默认的一般都是异步调用，加上 Sync 的是同步的阻塞的

3. 读取文件快不快很受cpu现在忙不忙影响。

4. 异步调用

   ```javascript
   console.time('async');
   console.log('async start');
   fs.readFile('./2012.txt', 'utf8', (err,result) => {
       if (err) throw err;
       console.log("async finish")
   })
   console.timeEnd('async');
   ```

5. 同步调用

   ```javascript
   console.time('sync');
   console.log('sync start');
   try {
       var data = fs.readFileSync('./2012.txt', 'utf-8');
       console.log('sync finish');
   } catch (err) {
       throw err;
   }
   console.timeEnd('sync');
   ```

## 文件编码和缓冲区Buffer

1. 读文件的时候没有传编码，默认的返回Buffer(缓冲区)
2. 读文本文件一般用 “utf8” 格式
3. **现在的开发叫做基于高度封装的开发**
4. 什么是缓冲区
   1. 缓冲区及时内存中操作数据的容器
   2. 只是数据容器而已
   3. 通过缓冲区可以很方便的操作二进制数据
   4. 而且在大文件操作室必须有缓冲区
5. 把文件看做一个大水桶，想移动文件的话相当于换了一个水桶，可以把水一次倒进另一个水桶中，但是文件太大内存受不了；文件流就相当于一瓢一瓢的去从第一个桶里舀水到第二桶里边去，缓冲区就相当于这个瓢，一次只能放20M，40M之类的这么多文件，多舀几次
6. 早期的 JS 只能操作字符串数据，最近才能读二进制文件，就要用到缓冲区的概念，文件取一部分，放到缓冲区，再取一部分直到结束
7. `buf.toString(编码格式)` buf 中的内容读成字符串
8. `var buffer = new Buffer(4)` 能装4个字节，`buffer.write('sssssssssss')` 只能存4个s，超过的就漏出去了。
9. `var buf2 = new Buffer(4)`，能装下int32,2字节只能装下int16，`buf2.writeInt32LE(2)` 无论写的数字是什么都会占4个字节的空间，int32 的限制是27亿
10. 一般我们用writeInt32Le就行了，网络传输用到32BE，int32是占四个内存空间，int8占一个，int16 占两个内存空间
11. base64 编码一般都是两个==

## 正则

1. 写正则的时候先找一个将要进行操作的字符串模板，然后一点一点修改它
2. `[00:01.22] 大家好` 复制他，修改之 `/\[(d{2})\:(d{2})\.(d{2})\]\s(.+)/`
3. . 表示任意非换行字符
4. 要是匹配之后还需要进一步操作的话，直接用 exec 执行，不用 test 测试可以节约一点性能，匹配结果第一项是字符串本身，$1 之类的才是我们自己的分组

## 写入文件

1. `JSON.stringfy({id = 10})` 序列化 JSON 字符串
2. `JSON.parse()` 反序列化
3. 写文件常见失败
   1. 意外错误
   2. 文件权限问题
   3. 找不到文件夹（不会自动帮你创建文件夹）
4. 写入文件的方式
   1. 异步文件写入 `fs.writeFile(file, data[, option],callback(err))`
   2. 同步文件写入 `fs.writeSync(file,data[,option])`
   3. 流式文件写入 `let streamWriter = fs.createWriteStream(path[,option])` 和 `streamWriter.write(data,callback)`
5. 同步的情况用try catch 捕获错误。
6. 默认写文件是覆盖过去，想要追加fs.appendFile

## 其他的文件操作

1. 很多系统移动和重命名文件用的都是相同的api

## 目录操作

1. 创建一个目录
   1. fs.mkdir(path[, model], callback)
   2. fs.mkdirSync(path[, model]);
2. 删除一个空目录
   1. fs.rmdir(path, callback)
   2. fs.rmdirSync(path)
3. 读取一个目录
   1. fs.readdir(path, callback(err, files))
   2. fs.readdirSync(path) // => 返回files

## 如何写递归

1. 先写一层的情况
2. 抽象递归参数
3. 找到突破点（避免死循环）
4. 递归就是自己调用自己，突破点就是找到那种不调用自己的情况
5. 数组的 sort() 就是根据 ASCII 码排序
6. `var a = new Array(2)` 声明一个长度为2的空数组