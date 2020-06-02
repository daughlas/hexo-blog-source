---
title: debounce 防抖
date: 
categories:
- 一段代码
tags:
- javascript
---

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>防抖函数</title>
  <style>
    #container {
      width: 100%;
      height: 200px;
      line-height: 200px;
      text-align: center;
      color: #fff;
      background-color: #444444;
      font-size: 30px;
    }
  </style>
</head>
<body>
  <h2>使用场景：</h2>
  <div>监听 window 的 resize、scroll</div>
  <div>监听鼠标的 mousedown、mouseover</div>
  <div>监听键盘的 keyup、keydown</div>
  <div>...</div>
  <div id="container"></div>
  <button id="btn">取消debounce</button>

<script>
var count = 1;
var container = document.getElementById('container');

function getUserAction(ev) {
  console.log(ev)
  console.log(this);
  container.innerHTML = count++;
  return count;
}

// 第 0 版
// container.onmousemove = getUserAction;

// 第一版
// function debounce(func, wait) {
//   var timeout;
//   return function() {
//     clearInterval(timeout)
//     timeout = setTimeout(func, wait)
//   }
// }

// 第二版
// 修复 this
// function debounce(func, wait) {
//   var timeout;
//   return function() {
//     var context = this;
//     clearInterval(timeout)
//     timeout = setTimeout(function() {
//       func.apply(context)
//     }, wait)
//   }
// }

// 第三版
// 修复event
// function debounce(func, wait) {
//   var timeout;
//   return function() {
//     var context = this;
//     var args = arguments;

//     clearInterval(timeout);
//     timeout = setTimeout(function() {
//       func.apply(context, args)
//     }, wait);
//   }
// }

// 第四版
// 让他可以立即执行
// function debounce(func, wait, immediate) {
//   var timer;

//   return function() {
//     var context = this;
//     var args = arguments;
      
//       if (timeout) {
//         clearTimeout(timer);
//       }
    
//     if (immediate) {
//       var callNow = !timer;
//       timer = setTimeout(function() {
//         timer = null;
//       }, wait)
//       if (callNow) {
//         result = func.apply(context, args)
//       }
//     } else {
//       timer = setTimeout(function() {
//         func.apply(context, args);
//       }, wait)
//     }
//   }
// }

// 第五版
// 增加一个取消的功能
function debounce(func, wait, immediate) {
  var timer;

  var debounced = function() {
    var context = this;
    var args = arguments;

    if (timer) {
      clearTimeout(timer)
    }
    if (immediate) {
      var callNow = !timer;
      timer = setTimeout(function() {
        timer = null;
      }, wait)
      if (callNow) {
        func.apply(context, args)
      }
    } else {
      timer = setTimeout(function() {
        func.apply(context, args)
      }, wait)
    }
  }

  debounced.cancel = function() {
    clearTimeout(timer);
    timer = null;
  }
  return debounced;
}
var setUserAction = debounce(getUserAction, 1000, true);
container.onmousemove = setUserAction;
var btn = document.getElementById('btn')
btn.onclick = function() {
  setUserAction.cancel()
}
</script>
</body>
</html>
```