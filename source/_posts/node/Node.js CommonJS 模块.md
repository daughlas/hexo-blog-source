---
title: Node.js CommonJS 模块
date: 
categories:
- Node
tags:
- Node.js
---

# Node 模块

1. Node.js 一个请求中有死循环，处理不完，线程就被阻塞了，后面的请求都得不到响应。
2. 只有异步和请求才会被放到线程池中。
3. Node.js 开发一定要注意
   1. 死循环
   2. 循环依赖
4. 严谨不严谨是写代码的时候有没有考虑可能报错。
5. Common.js 标准，包括
   1. 一个文件一个模块
6. 在 node 中文件是直接读取出来的，不用下面的老结构，去掉 define 就是 Common.js 的规范了

```javascript
// CMD 规范
define(function(require, module, exports) {
    function convert(input) {
        return parseFloat(input);
    }

    function add(a, b) {
        return convet(a) + convert(b)
    }

    function substract(a, b) {
        return convet(a) - convert(b)
    }

    function multiply(a, b) {
        return convet(a) * convert(b)
    }

    function divide(a, b) {
        return convet(a) / convert(b)
    }
    // 第一种方法
    exports.add = add;

    // 第二种优先级更高的方法，自动化属性，相当于add: add，下面这个是ES6的语法
    module.exports = {add, substract, multiply, divide}
})
```

7. 模块的类型
   1. 核心模块：系统内置的
   2. 第三方模块：
   3. 文件模块：自己写的
8. 全局对象
   1. __dirname：获取当前脚本所在目录的路径
   2. __filename：获取当前脚本的文件路径
9. 注意：如果模块当中定义了相对路径，node 会以入口文件（调用这个模块的文件）的路径为基准，进行相对路径的计算。
10. 所有的文件操作最安全的方法就是就是使用绝对路径，还有一种方法就是用 `__dirname + '/../list'`
11. 模块内部实际上是一个私有的空间，外边不能随便拿到里边的内容
12. 模块内全局环境：
    1. module：模块对象
       1. module.exports 是个对象
       2. module.parents 当前模块被谁载入了
       3. module.loaded 没有执行完呢
       4. module.children 当前脚本加载的所有子模块
       5. module.path 是模块加载的顺序。
    2. exports：映射到 module.exports 的别名
    3. require()
       1. require.cache
       2. require.extensions
       3. require.main
       4. require.resolve
13. exports = module.exports = {}
14. require 
    1. 加载目录规则
    2. 加载机制：如果不写拓展名，先找这个名字的 js 文件，再找 json 文件， 再找.node 文件，再然后如果有个文件夹同名，会去加载这个文件夹下面的 index.js 和 package.json 中的 main 指向的文件 （相对更优先）。
15. require 不仅仅可以载入 JS 模块，也可以载入 JSON 对象。
16. 因为 require 默认会加载系统核心模块和node_modules 中的第三方模块，所以同目录下的文件不能直接写文件名，要加"./"。
17. 读文件一定要用物理路径，加上__dir这种
18. 模块缓存
    1. 加载模块是个阻塞操作
    2. 模块一加载，就会缓存起来。
    3. 看缓存，就用定时器不停查看
    4. 清除缓存 `delete require.cache.[模块名]`或者高级的 `Object.keys(require.cache).forEach((key)=>{delete require.cache[key]})`;
19. 一般我们使用的时候很少暴露属性，都是暴露行为的，可控程度更高 。

```javascript
console.log("执行了");
module.exports = new Date();
```

上面这样写，第一次的时候会执行这段代码，再引用就不会了，解决的办法如下

```javascript
module.exports = () => {
    console.log("执行了");
    return new Date();
}
```

20. 自己写缓存的时候注意：缓存的是 module 对象，返回的都是 module.exports 成员

