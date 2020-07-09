---
title: 学习 React Hooks
date: 
categories:
- 前端框架
tags:
- vue
---

## 1 React 简介和缘起

### React 特点

* 声明式写法
* 组件化
* 一次学习，随处编写
* 大厂加持、简单易懂、最流行

## 2 useState Hook

hook 是一个特殊的函数，允许你勾入 react 的特性

useState 记录函数内组件的状态，并且在每次组件更新的时候记录状态值

```javascript
const [ like, setLike ] = useState(0)
```

定义了 state 的一个属性 like

定义了一个更新 like 属性的方法 setLike

定义了 like 的初始值为 0

函数退出的时候 state 中的变量会保留

## 3 useEffect 不需要清除的 Effect

* 无需清除的 Effect
  * 发送网络请求
  * 手动变更 DOM
  * 记录日志
  * 例子：使用 useEffect 使用 DOM 完成标题更新



## 4 useEffect 需要清除的 Effect

* 需要清除的Effect
  * 定时器
  * DOM 事件监听
  * 例子：鼠标点击记录点击的位置

useEffect 的返回值，会在组件卸载和组件更新之前执行



## 5 useEffect 可控 effect

```javascript
useEffect(() => {
    console.log('useEffect 被调用了')
    document.title = `点击了${like}次`
})
```

不传第二个参数的时候，useEffect 会在初始化、每次 DOM 更新之后调用

```javascript
useEffect(() => {
    console.log('useEffect 被调用了')
}, [])
```

第二个参数传个空数组，只在初始化的时候调用一次

```javascript
useEffect(() => {
    console.log('useEffect 被调用了')
    document.title = `点击了${like}次`
}, [like])
```

第二个参数传个 state 的数组，只在初始化和对应 state 变化的时候才调用

## 3-7 自定义 Hook

* 将组建逻辑提取到可重用的函数中
* 之前两种方法：render props，一个叫高阶组件
* 例子使用自定义 Hook 抽象 鼠标跟踪器

例子

```javascript
import React, { useState, useEffect } from 'react'

const useMousePosition = () => {

  const [ position, setPosition ] = useState({x: 0, y: 0})

  useEffect(() => {
    const updateMouse = e => {
      setPosition({x: e.clientX, y: e.clientY})
    }
    document.addEventListener('click', updateMouse)
    return () => {
      document.removeEventListener('click', updateMouse)
    }
  })

  return position
}

export default useMousePosition
```

## 6 HOC的概念和缺点

### 概念

高阶组件就是一个函数，接受一个组件作为参数，返回一个新的组件，可以添加一些属性

```javascript
// high order component
import React from 'react'
import axios from 'axios'

const withLoader = (WrappedComponent, url) => {
  return class LoaderComponent extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        data: null,
        isLoading: false
      }
    }
    componentDidMount() {
      this.setState({
        isLoading: true,
      })
      axios.get(url).then(result => {
        this.setState({
          data: result.data,
          isLoading: false
        })
      })
    }
    render() {
      const { data, isLoading } = this.state
      return (
        <>
          { (isLoading || !data) ? <p>data is loading</p> :
            <WrappedComponent {...this.props} data={data} isLoading={isLoading} />
          }
        </>
      )
    }
  }
}

export default withLoader
```


## 7 Hook 规则和其他 Hook

* 只在最顶层使用 Hook，不要再循环、条件或者嵌套函数中调用 Hook，Hook 每次在渲染中以同样的顺序被调用
* 只在 React 函数中使用 Hook，Hook 中可以调用其他 Hook

### 其他 Hooks 

* 搜索 Hooks API 索引
* useHooks.com 官方推荐的一个网站

* useContext 拿到 Provider 和 Consumer
* useReducer，甚至可以替代 Redux
* useCallback 性能调优，避免每次组件的变量重新创建，多次渲染的时候可以避免反复新建对象