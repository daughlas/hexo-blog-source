---
title: flutter 概述
date: 
categories:
- 混合开发
tags:
- 混合开发
- flutter
---


## 如何使用 fluttr 包和插件

pub.dartlang.org 网站

flutter_color_plugin



## StatelessWidget 与基础组件

不需要状态改变的 widget

内部没有需要改变的状态，或者根据状态改变来重新渲染自己

### Container 容器

### Text 文本

### Icon 图标

### CloseButton 关闭按钮

### BackButton 返回按钮

### Chip

### Divider 分割线

### Card 卡片

### AlertDialog 弹框



## StatefulWidget 与基础组件

StatefulWidget 它并没有一个 build 方法去返回 Widget，而是多了一个 createState 方法返回 `_ + 类名 + State` 对象，而 build 方法则包含在这个`_ + 类名 + State`类当中。



Widget 需要依据数据才能完成构建，而对于 StatefulWidget 来说，其依赖的数据在 Widget 生命周期中可能会频繁地发生变化。由 State 创建 Widget，以数据驱动视图更新，而不是直接操作 UI 更新视觉属性，代码表达可以更精炼，逻辑也可以更清晰。

### MaterialApp

材料设计 App

通常放在页面根节点

### Scaffold

封装了AppBar，底部导航栏，侧边栏

帮助实现App 整体布局

### AppBar

顶部导航栏

### BottomNavigationBar

底部导航栏

### RefreshIndicator

### Image

### TextField

输入框

### PageView



* * * Wrap 从左向右排列，可以自动换行
    *  Flow 不好用，少用
  
* ParentDataWidget
  * Positioned 固定位置，通常和 Stack 搭配使用
  * Flexible
    *  Expanded 展开，在父容器中展开多大