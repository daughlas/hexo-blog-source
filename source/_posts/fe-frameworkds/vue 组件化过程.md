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

### 具体步骤

* init 初始化过程
* $mount 挂载方法
* compile
  * parse 生成 ast
  * optimize 优化，标记静态节点，虚拟 DOM 比对的时候直接跳过
  * generate 生成字符串函数，用 new Fuction 调用
* render function
  * 创建流程：
    * 生成虚拟DOM
      * patch方法
        * patchVnode
        * updateChildren
  * 更新流程
    * getter，哪些数据和哪些组件有依赖关系
    * setter
    * watcher


递归渲染

组件层级特别多，渲染可能会慢


## 虚拟 DOM的优势

* 提供一个不错的速度
* 可以提供跨平台能力





