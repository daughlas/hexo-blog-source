---
title: Vue.js 渲染初始化渲染过程
date: 
categories:
- 前端框架
tags:
- vue
---

## Vue.js 渲染初始化渲染过程


new Vue => init => $mount => compile => render => vnode => patch => DOM

递归渲染

组件层级特别多，渲染可能会慢

```javascript
new Vue()
init()
$mount()
compile()
render()
vnode
patch()
if (Component Vnode) {
	new Vue()
} else {
	DOM
}

```

## 虚拟 DOM的优势

* 提供一个不错的速度
* 可以提供跨平台能力





