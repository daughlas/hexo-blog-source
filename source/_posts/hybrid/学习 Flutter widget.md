---
title: 学习 Flutter widget
date: 
categories:
- 混合开发
tags:
- 混合开发
- flutter
---

# 学习 Flutter widget

##  Text

* textAlign: TextAlign.left
* maxLines: 1
* overflow: TextOverflow.ellipsis，TextOverflow.fade
* style: TextStyle
  * fontSize: 25.0
  * color: Colors.pink，Color.fromARGB(255, 255, 0, 0)
  * decorationStyle: TextDecorationStyle.solid



## Container

* alignment:
  * Alignment.center 水平垂直居中
  * Alignment.bottomCenter 在底部局中
  * Alignment.bottomCenter 下左对齐
  * Alignment.centerLeft 垂直居中，水平居左 
  * ...
* width
* height
* color: Colors.black
* padding:
  *  EdgeInsets.all(10.0)
  * EdgeInsets.fromLTRB(10.0,  30.0, 10.0, 10.0)
* margin: 跟 margin 的值一样
* decoration: BoxDecoration 
  * 注意，这玩意和 color 不能同时存在
  * gradient: LinearGradient(colors: [Colors.lightBlue, , Colors.greenAccent, Colors.purple])
  * border: Border.all(width: 2.0, color: Colors.red)



## Image

* asset 资源目录中
* file 本地路径
* memory 内存图片
* network 网络图片
* fit: BoxFit
  * contain 尽量撑满容器，保持原图的比例和现实完全
  * fill 容器要被图片填满，不管图片拉伸不拉伸
  * fitWidth 图片的比例不变，横向是充满的，总想可以裁切
  * fitHeight 图片比例不变，纵向要充满
  * cover：充满整个容器，但是图片不变形
  * scaleDown 保持原图片的大小
* 图片混合模式
  * color: Colors.greenAccent
  * colorBlendMode:, BlendMode
    * darken
    * lighten
    * modulate
    * ...
* repeat: ImageRepeat
  * noRepeat
  * repeat 以中间为基础，不断重复
  * repeatX 只横向重复
  * repeatY 纵向重复