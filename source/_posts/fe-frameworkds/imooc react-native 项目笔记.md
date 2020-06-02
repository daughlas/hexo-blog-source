---
title: imooc react-native 项目笔记
date: 
categories:
- 前端框架
tags:
- react
- react-native
- imooc 实战
---

# imooc react-native 项目笔记

## React 基础知识

> 声明式与命令式

```
命令式编程：命令“机器”如何去做事情（how），这样不管你想要的是什么（what），他都会按照你的命令实现
声明式编程：高速“机器“你想要的是什么（what），让机器想出如何去做(how)
```

### 如何返回多个顶级标签

```
class HelloMessage extends Component {
	render() {
		let views = []
		views.push(<h1>Hello world!</h1>)
		views.push(<h1>I am Lv Jiawen.</h1>)
	}
}
ReactDOM.render(
	<HelloMessage />,
	document.getElementById('root')
)
```

### 渲染子节点

```
class Comp extends Component {
	render() {
		return (
			<ul>
				{
					React.children.map(this.props.children, (child) => {
						return <h1>{child}</h1>
					})
				}
			</ul>
		)
	}
}

ReactDOM.render(
	<Comp>
		<span>hello</span>
		<span>world</span>
		<span>lvjiawen</span>
	</Comp>,
	document.getElementById('root')
)
```

### PropTypes

```
import PropTypes from 'prop-types'
class Comp extends Component {
	static propTypes = {
		title: PropTypes.string.isRequired
	}
	render() {
		// ....
	}
}
```

### 默认属性

```
class Comp extends Component {
	static defaultProps = {
		shortName: 'MyTitle'
	}
	render() {
		// ....
	}
}
```



### ref属性（获取真实 DOM）

mount 之后才能拿到 $refs

```
class Alert extends React.Component {
	showAlert(message) {
		alert(`Debug:${message}`)
	}
	render() {
		return null
	}
}

class MyTitle extends Component {
	onClick = () => {
		this.$refs.alert.showAlert('MyTitle')
	}
	render() {
		return (
			<div>
				<h1 onClick={this.onClick}>Click me</h1>
				<Alert ref='alert'/>
			</div>
		)
	}
}
```

### state

用户交互之后，组件自身会改变的一些属性

props 是组件自身无法改变的属性



### 组件生命周期

* IOS

  * (void) viewWillAppear: (BOOL)animated
  * (void) viewDidLoad
  * (void) viewWillDisappear: (BOOL)animated

* Android  Activity 提供了

  * onCreate()
  * onStart()
  * onResume()
  * onPause()
  * onStop()
  * onDestroy()

* react

  * 初始化阶段
    1. **constructor**
    2. static getDerivedStateFromProps()
    3. **componentWillMount()** / UNSAFE_componentWillMount()
    4. **render()**
    5. **componentDidMount()**
  * 更新阶段（props 或者 state 的改变可能会引起组件的更新，组件重新渲染的过程中会调用）
    1. componentWillReceiveProps()
    2. UNSAFE_componentWillReceiveProps()
    3. static getDerivedStateFromProps()
    4. shouldComponentUpdate()
    5. **componentWillUpdate()** / UNSAFE_componentWillUpdate()：内存里边，数据已经是最新的了，界面还没有更新
    6. **render()**
    7. getSnapshotBeforeUpdate()
    8. **componentDidUpdate()**
  * 卸载阶段
    1. componentWillUnmount()
  * 错误处理
    1. componentDidCatch()

  

  ### render

  * 返回
    * react 元素
    * 字符串、数字
    * null
    * 布尔值

  

  ### componentDidMount

  组件被装配之后立即调用

  componentDidMount 在 render 方法之后执行的，componentDidMount 可以获取到真是的 DOM 节点

  ### shouldComponentUpdate

  初始化渲染不会调用该方法

  ```
  shouldComponentUpdate(nextProps, nextState) {
  	return true / false
  }
  ```

  ### componentDidUpdate

  ### componentWillUnmount

  组件从DOM 中一处的时候立即被调用。清除定时器，在 componentDidMount 创建的一些东西。



## React-native 布局知识

react-native 中，宽高没有单位，代表设备的独立像素

### react-native flex box 与 web css flex box 的不同

* 在 react-native 中 flexDirection 默认为 ’column'，在css 中 flex-direction 默认为 row
* 在 react-native 中 alignItems 默认为 ’stretch'，在css 中 align-items 默认为 'flex-start'
* 在 react-native 中 flex 只接受一个参数，在css 中 flex 默认为 '2 2 10%'
* react-native 不支持 align-content, flex-basis, order, flex-flow, flex-grow, flex-shrink

### 父亲视图属性

* flexDirection：row, column, row-reverse, column-reverse
* flexWrap：wrap, nowrap
* justifyContent: flex-start, flex-end, center, space-between, space-around
* alignItems: flex-start, flex-end, center, stretch

### 子视图属性

* alignSelf：重写父视图中alignItems
* flex：定义一个元素可伸缩的能力，默认为0



## React-navigation

### 什么是导航器？

导航器也可以堪称是一个普通的 react 组件