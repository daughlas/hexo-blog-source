---
title: 学习 Flutter 布局
date: 
categories:
- 混合开发
tags:
- 混合开发
- flutter
---


## Row

* children： <Widget>[]
* 灵活的布局的时候，children 元素要实现类似 flex 的布局，外边包一层  Expanded。



## Column

* crossAxisAlignment
  * CrossAxisAlignment.start
  * CrossAxisAlignment.end
  * CrossAxisAlignment.center
* mainAxisAlignment
  * mainAxisAlignment.center
* 结合 Expanded 使用



## Stack

* 从左上角开始层叠
* 两个东西层叠的时候，可以用对齐属性
* alignmen 
  * x 从 0 到 1
  * y 从 0 到 1
* Positioned



## ListView 

* scrollDirection Axis
  * horizontal 横向列表
  * vertical 纵向列表
* ListView.builder(itemCount: items.length, itemBuilder: (context, index) {return ListTile(title: Text('items[]$index]'))})

### ListTile

* leading(icon: Icon(Icons.home), title: Text('首页')))



## GridView

* GridView.count
  * padding EdgeInsets.all(20.0)
  * crossAxisSpacing: 10.0,
  * crossAxisCount: 3,
  * children: <Widget>[]



## Card

```
Card(
	child: Column(
		children: <Widget>[
			ListTile(
				title: Text('00001', textStyle: TextStyle(fontWeight: FontWeight.w500)),
				subtitle: Text('二级标题'),
				leading: Icon(Icons.account_box, color: Colors.lightBlue)
			),
			Divider()
			ListTile(
				title: Text('00001', textStyle: TextStyle(fontWeight: FontWeight.w500)),
				subtitle: Text('二级标题'),
				leading: Icon(Icons.account_box, color: Colors.lightBlue)
			)
		]
  )
)
```
