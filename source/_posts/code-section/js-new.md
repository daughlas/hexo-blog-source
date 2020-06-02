---
title: JS - new
date: 
categories:
- 一段代码
tags:
- javascript
---

# JS - new
## new 操作符都做了什么
* 创建了一个全新的对象。
* 把这个对象链接到原型对象上
* 生成的新对象会绑定到函数调用的this。
* 如果这个函数不返回任何东西，那么就会默认return this

## 代码

```javascript
function myNew(ctor) {
  if (typeof ctor !== 'function') {
    throw 'new Operator need a function param'
  }
  // ES6
  myNew.target = ctor

  var newObj = Object.create(ctor.prototype)
  var argsArr = [].slice.call(arguments, 1)
  var ctorReturnResult = ctor.apply(newObj, argsArr)
  var isObject = typeof ctorReturnResult === 'object' && ctorReturnResult !== null
  var isFunction = typeof ctorReturnResult === 'function'
  if (isObject || isFunction) {
    return ctorReturnResult
  }
  return newObj
}
```
