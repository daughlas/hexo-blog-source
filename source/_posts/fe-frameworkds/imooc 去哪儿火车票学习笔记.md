---
title: imooc 去哪儿火车票学习笔记
date: 
categories:
- 前端框架
tags:
- react
- redux
- imooc 实战
- 待续
---

## PWA

* 渐进式网络应用
* 可控的静态缓存
* 离线访问能力
* 优化载入速度

### 效率类

* iconfont
* snippets

### 原则类

* 职责分离
  * 两耳不闻窗外事
  * 模块解耦
  * 优化可维护性



## 新特性

* Context
* ContextType
* Lazy
* Suspense
* memo

### Context

* Context 提供了一种方式，能够让数据在组件树中传递，而不必一级一级手动传递
* 损失了一部分独立性
* context
  * provider
  * consumer
* API
  * createContext(defaultValue?)



```jsx
import React, {Component, createContext} from 'react';
const BatteryContext = createContext()

class Middle extends Component {
  render() {
    return <Leaf/>
  }
}

class Leaf extends Component {
  static contextType = BatteryContext;

  render() {
    const battery = this.context
    return (
      <h1>Battery: {battery}</h1>
    )
  }
}

class App extends Component {
  // 或者在组件的 constructor 之中声明 this.state
  // 这个是新写法
  state = {
     battery: 60
  }

  render() {
    const { battery } = this.state;
    return (
      <BatteryContext.Provider value={battery}>
          <button onClick={() => this.setState({battery: battery-1})}>change battery</button>
          <Middle/>
      </BatteryContext.Provider>
    );
  }
}

export default App;

```



### Lazy 和 Suspense

#### 背景

暂时没有使用的资源 => 延迟加载

#### 途径

* webpack code spliting

* import
  * import from 静态导入模块
  * import('XXX组件').then(.........)
* lazy 封装了组件的导入行为



```jsx
import React, {Component, lazy, Suspense } from 'react';

const About = lazy(() => import(/* webpackChunkName: "About" */'./About.jsx'))

// ErrorBoundary 后台组件渲染错误
// 利用 componentDidCatch

class App extends Component {
  state = {
    hasError: false
  }

  // componentDidCatch() {
  //   console.log('error')
  //   this.setState({
  //     hasError: true
  //   })
  // }

  static getDerivedStateFromError() {
    return {
      hasError: true
    }
  }

  render() {
    if (this.state.hasError) {
      console.log('has error')
      return (<div>Error</div>)
    } else {
      console.log('no error')
      return (
        <div>
          <Suspense fallback={<div>loading</div>}>
            <About/>
          </Suspense>
        </div>
      )
    }
    
  }
}

export default App;
```



### memo

下面这样写，即使 Foo 组件不需要渲染也会一直调用 Foo 的render 方法

```jsx
import React, {Component } from 'react';
import './App.css';

class Foo extends Component {
  render() {
    console.log('Foo render')
    return null
  }
}

class App extends Component {
  state = {
    count: 0
  }
  render() {
    return (
      <div>
        <button onClick={() => this.setState({count: this.state.count+1})}>加一</button>
        <Foo name="Mike"/>
      </div>
    )
  }
}

export default App;

```

优化一下，试用一下 shouldComponentUpdate：

```jsx
import React, {Component } from 'react';
import './App.css';

class Foo extends Component {
  shouldComponentUpdate(nextProps, nextState) {
    if (nextProps.name === this.props.name) {
      return false
    } else {
      return true
    }
  }

  render() {
    console.log('Foo render')
    return null
  }
}

class App extends Component {
  state = {
    count: 0
  }
  render() {
    return (
      <div>
        <button onClick={() => this.setState({count: this.state.count+1})}>加一</button>
        <Foo name="Mike"/>
      </div>
    )
  }
}

export default App;

```

在优化一下，使用 PureComponent，注意，只有传入属性本身的对比，如果属性是对象，对象内部的属性变化，就无能为力了。

```
import React, {Component, PureComponent } from 'react';
import './App.css';

class Foo extends PureComponent {
  render() {
    console.log('Foo render')
    return null
  }
}

class App extends Component {
  state = {
    count: 0
  }
  render() {
    return (
      <div>
        <button onClick={() => this.setState({count: this.state.count+1})}>加一</button>
        <Foo name="Mike"/>
      </div>
    )
  }
}

export default App;
```

来展示一下 bug 的代码，本该变化的结果没变化，注意只有props 的第一级变化才会触发变化

```jsx
import React, {Component, PureComponent } from 'react';
import './App.css';

class Foo extends PureComponent {
  render() {
    console.log('Foo render')
    return (
    	<div>{this.props.person.age}</div>
    )
  }
}

class App extends Component {
  state = {
    count: 0,
    person: {
    	age: 1
    }
  }
  render() {
    const person = this.state.person
    return (
      <div>
        <button onClick={() => {
          person.age++;
          this.setState({person})
          }}>
          加一
        </button>
        <Foo person={person}/>
      </div>
    )
  }
}

export default App;
```

使用 无状态 组件 Foo 和 memo 的解决方案

```jsx
import React, {Component, memo } from 'react';
import './App.css';

const Foo = memo(
  function Foo (props) {
    console.log('Foo render')
    return (
      <div>{props.count}</div>
    )
  }
)

class App extends Component {
  state = {
    count: 0,
  }
  render() {
    return (
      <div>
        <button onClick={() => this.setState({count: this.state.count+1})}>
          加一
        </button>
        <Foo name="Mike"/>
      </div>
    )
  }
}

export default App;
```

推论：拆分的越细，prop 和 state 越简单，越容易写成 pureComponent 或者 memo 的形式



## Hooks

### 什么是 hooks

> Hooks let you use state and other React features without writing a class

### 代码

#### 原始写法

```jsx
class Foo extends React.Component {
	state = {
		size: [window.innerWidth, window.innerHeight]
	}
	
	onResize = () => {
    this.setState({
      size: [window.innerWidth, window.innerHeight]
    })
	}
  
  componentDidMount() {
    window.addEventListener('resize', this.onResize)
    document.title = this.state.size.join('x')
  }

	componentDidUpdate() {
    document.title = this.state.size.join('x')
  }

	componentWillUnmout() {
    window.removeEventListener('resize', this.onResize)
  }

	render() {
    return(
      <div>
        {this.state.size[0]} X {this.state.size[1]}
      </div>
    )
	}
}
```

#### 原始写法优化（渲染组件）

```
以后补充下
```

#### 原始写法优化（高阶组件）

```
以后补充下
```



### 类组件不足

* 状态逻辑复用难
  * 缺少复用机制
  * 渲染属性和高阶组件导致层级冗余
* 趋向复杂难以维护
  * 生命周期函数混杂不相干的逻辑
  * 想干逻辑分散在不同的生命周期中
* this 指向困扰
  * 内联函数过度创建新句柄，导致子组件过度渲染，比如传递`() => {}`
  * 类成员函数不能保证 this，必须使用 this 属性来声明回调函数
* Hooks 优势：优化类组件的三大问题
  * 函数组件无 this 问题，函数内部没有实例化的概念，就没有了 this 指向的问题
  * 自定义Hook 方便复用状态逻辑（hooks 是函数组件内部调用的特殊函数，最常见的有 useEffect 和 useState）
  * 副作用的关注点分离（除了数据向视图转化之外的其他东西，例如绑定解绑事件、发送请求、访问原生 DOM 元素、本地持久化缓存....，每个 useEffect 只处理一种副作用）

### 写法优化（Hooks 写法）

```jsx
function Foo() {
  const size = useSize();
  
  useEffect(() => {
  	document.title = size.join("x")
  })
  
  return (
  	<div>
      {size[0]} * {size[1]}
    </div>
  )
}
```



### 使用 State Hooks

#### useState

class 组件

```jsx
import React, {Component } from 'react';
import './App.css';

class App extends Component {
  state = {
    count: 0
  }
  render() {
    const { count } = this.state
    return (
      <div>
        <button onClick={() => this.setState({count: count + 1})}>+ 1</button>
        <h1>{ count }</h1>
      </div>
    )
  }
}

export default App;
```

hooks 组件

```jsx
import React, { Component, useState } from 'react'

function App() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() =>setCount(count+1)}>+ 1</button>
      <h1>count: { count }</h1>
    </div>
  )
}

export default App
```



注意：

* 调用规规矩矩来
* useState 可以传入一个函数，来进行延迟初始化，处理复杂的逻辑，避免每次都执行复杂逻辑 



### 使用 Effect Hooks

#### 副作用

* 绑定事件
* 网络请求
* 访问 DOM

#### 副作用时机

* Mount 之后，之前做法componentDidMount
* Update 之后，之前做法componentDidUpdate
* Unmout 之前，之前做法componentWillUnmount
* 以后都是 useEffect

#### useEffect

* 组件每次 渲染 之后调用
* 并且根据自定义状态决定调用或者不调用
* useEffect 的第二个参数
  * 不传数组，每次运行之后都会执行 useEffect
  * 传空数组，只 mount 和 unmonut 的时候运行
  * 首次渲染和数组中的元素变化的时候会去执行
  * 也就是说，只有数组的每一项都没变的情况下，useEffect 才不会执行，第一次肯定会执行

```jsx
import React, { useState, useEffect } from 'react'

function App(props) {
  const [count, setCount] = useState(0);
  const [size, setSize] = useState({
    width: document.documentElement.clientWidth,
    height: document.documentElement.clientHeight,
  })

  const onResize = () => {
    setSize({
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight,
    })
  }

  useEffect(() => {
    console.log("count:", count)
  }, [count])

  useEffect(() => {
    document.title = count
  })

  useEffect(() => {
    console.log("绑定事件")
    window.addEventListener("resize", onResize, false);
    return () => {
      console.log("解绑事件")
      window.removeEventListener("resize", onResize, false);
    }
  }, [])

  return (
    <div>
      <button onClick={() =>setCount(count+1)}>+ 1</button>
      <h1>count: { count }，size:{size.width} * {size.height}</h1>
    </div>
  )
}

export default App
```



### 4-4 使用 Context Hooks

```jsx
import React, { useState, createContext, useContext } from 'react'

const CountContext = createContext();

function App(props) {
  const [count, setCount] = useState(0);

  return (
    <div>
      <div>
        <button onClick={() =>setCount(count+1)}>+ 1</button>
      </div>
      <CountContext.Provider value={count}>
        <Counter></Counter>
      </CountContext.Provider>
    </div>
  )
}

function Counter() {
  const count = useContext(CountContext)
  return (
    <h1>{count}</h1>
  )
}

export default App
```

### 4-5 使用 Memo、Callback Hooks

useMemo 判断依赖是否改变，进而判断是否触发一段逻辑

useMemo 渲染期间调用

```jsx
import React, { useState, useMemo } from 'react'

function App(props) {
  const [count, setCount] = useState(0);

  const double = useMemo(() => {
    return count * 2;
  }, [count])

  return (
    <div>
      <div>
        <button onClick={() =>setCount(count+1)}>+ 1</button>
      </div>
      <div>
        double: {double}
      </div>
      <Counter count={count}></Counter>
    </div>
  )
}

function Counter(props) {
  return (
    <h1>{props.count}</h1>
  )
}

export default App
```



## Redux



