---
title: Redux 基础
date: 
categories:
- 前端框架
tags:
- react
- redux
---

# Redux 学习笔记

## Redux 概念简述

 react 只是一个数据层的框架，还需要一个数据层的框架与之配合

Redux = Reducer + Flux

## Redux 的工作流程

### 图书馆类比 Redux

* React Components（借书的用户）
* Action Creators（我要借什么书）
* Store（图书馆管理员，记不住所有书的情况）存储数据的公共区域
* Reducers 图书管理员，手中记录所有图书的账本，借书的时候查看一下，有没有这本书，还书的时候查看一下，书放在哪儿

### 工作流

* ActionCreaters => Store <=> Reducers

#### 获取数据

* ActionCreaters dispatch action 到 store 中，告诉 Store 组件要或去数据
* Sotre 知道了你要获取数据，Store 到 Reducers 里边去查一下应该返回什么样的数据
* Store 将数据返回给组件

#### 修改数据

* ActionCreaters dispatch action 到 store 中，告诉 Store 组件要或去修改数据
* Store 知道了你要修改数据，他问一下 Reducer Store 应该如何修改数据

* Store 更改完数据之后，告诉 React Component 数据改完了，你可以重新获取数据了
* React Component 重新获取数据

## redux 使用

src/ToDoList.js

```javascript
import React, { Component } from 'react'
import 'antd/dist/antd.css'
import { Input, Button, List } from 'antd'
import store from './store'

class TodoList extends Component {
  constructor(props) {
    super(props)
    this.state = store.getState()
    this.handleInputChange = this.handleInputChange.bind(this)
    this.handleStoreChange = this.handleStoreChange.bind(this)
    this.handleBtnClick = this.handleBtnClick.bind(this)
    store.subscribe(this.handleStoreChange)
  }
  render() {
    return (
      <div style={{padding: 10}}>
        <Input 
          value={this.state.inputValue} 
          onChange={this.handleInputChange} placeholder="todo info"
          style={{width: 300, marginRight: 20}}></Input>
        <Button onClick={this.handleBtnClick} type="primary">保存</Button>
        <List
          style={{marginTop: 10, width: 380}}
          bordered
          dataSource={this.state.list}
          renderItem={(item, index) => (
            <List.Item onClick={this.handleItemItemDelete.bind(this, index)}>
              {item}
            </List.Item>
          )}
        />
      </div>
    )
  }

  handleInputChange(e) {
    const action = {
      type: 'change_input_value',
      value: e.target.value
    }
    store.dispatch(action)
  }

  handleStoreChange() {
    this.setState(store.getState())
  }

  handleBtnClick() {
    if (!this.state.inputValue) {
      alert('请填写内容')
      return
    }
    const action = {
      type: 'add_todo_item',
      value: this.state.inputValue
    }
    store.dispatch(action)
  }

  handleItemItemDelete(index) {
    const action = {
      type: 'delete_todo_item',
      value: index
    }
    store.dispatch(action)
  }
}

export default TodoList
```

src/store/index.js

```javascript
import { createStore } from 'redux'
import reducer from './reducer'

const store = createStore(
  reducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
)

export default store
```



src/store/reducer.js

注意：**reducer 可以接受 state，但是绝对不能修改 state**

```javascript
const defaultState = {
  inputValue: '',
  list: []
}

// reducer 可以接受 state，但是绝对不能修改 state
export default (state = defaultState, action) => {
  if (action.type === 'change_input_value') {
    const newState = JSON.parse(JSON.stringify(state))
    newState.inputValue = action.value
    return newState
  }
  if (action.type === 'add_todo_item') {
    const newState = JSON.parse(JSON.stringify(state))
    newState.list.push(action.value)
    newState.inputValue = ''
    return newState
  }
  if (action.type === 'delete_todo_item') {
    const newState = JSON.parse(JSON.stringify(state))
    newState.list.splice(action.value, 1)
    return newState
  }
  return state
}
```



### ActionTypes 的拆分

src/store/actionTypes.js

这样可以让我们更容易发现错误，常量和变量写错了，代码会迅速报错，但是写一个字符串就很难排查错误了

```javascript
export const CHANGE_INPUT_VALUE = 'change_input_value'
export const ADD_TODO_ITEM = 'add_todo_item'
export const DELETE_TODO_ITEM = 'delete_todo_item'
```



### 使用 actionCreator 统一创建 action

不正规方法

```javascript
const action = {
  type: DELETE_TODO_ITEM,
  value: index
}
store.dispatch(action)
```

正规的方法

```javascript
import { CHANGE_INPUT_VALUE } from './actionTypes'
export const getInputChangeAction = (value) => {
  return {
    type: CHANGE_INPUT_VALUE,
    value
  }
}
```



* 这样可以提高代码的可维护性
* 方便之后测试

### ruducer 补充说明

#### 原则一 stroe 是惟一的

#### 原则二 只有 store 能够改变自己的内容

```javascript
export default (state = defaultState, action) => {
  if (action.type === 'change_input_value') {
    const newState = JSON.parse(JSON.stringify(state))
    newState.inputValue = action.value
    return newState
  }
  return state
}
```

注意：此时我们返回的是一个新的对象，我们没有在 reducer 里边修改 store，reducer 把这个新的对象返回给了 store，store 自己更新了这个数据。

reducer 可以接受 state，但是绝不能修改 state

### 原则三 reducer 必须是纯函数

纯函数：

* 给定固定的输入，就一定会有固定的输出
* 而且不会有副总用

### reducx 核心 API

* createStore

* store.dispatch(action)
* store.getState()
* store.subscribe(() => {})



## Redux 高级

### UI 组件与容器组件的拆分

* UI 组件：傻瓜组件，处理渲染

```javascript
import React, {Component} from 'react'
import { Input, Button, List } from 'antd'

class TodoListUI extends Component {
  render() {
    return (
      <div style={{padding: 10}}>
        <Input 
          value={this.props.inputValue} 
          onChange={this.props.handleInputChange}
          placeholder="todo info"
          style={{width: 300, marginRight: 20}}></Input>
        <Button onClick={this.props.handleBtnClick} type="primary">保存</Button>
        <List
          style={{marginTop: 10, width: 380}}
          bordered
          dataSource={this.props.list}
          renderItem={(item, index) => (
            <List.Item onClick={() => {this.props.handleItemItemDelete(index)}}>
              {item}
            </List.Item>
          )}
        />
      </div>
    )
  }
}

export default TodoListUI
```

* 容器组件：聪明组件，处理逻辑

```javascript
import React, { Component } from 'react'
import 'antd/dist/antd.css'
import store from './store'
import TodoListUI from './TodoListUI'
import { getInputChangeAction, getAddItemAction, getDeleteItemAction } from './store/actionCreators'

class TodoList extends Component {
  constructor(props) {
    super(props)
    this.state = store.getState()
    this.handleInputChange = this.handleInputChange.bind(this)
    this.handleStoreChange = this.handleStoreChange.bind(this)
    this.handleBtnClick = this.handleBtnClick.bind(this)
    this.handleItemItemDelete = this.handleItemItemDelete.bind(this)
    store.subscribe(this.handleStoreChange)
  }
  render() {
    return (
      <TodoListUI
        inputValue={this.state.inputValue}
        handleInputChange = {this.handleInputChange}
        handleBtnClick={this.handleBtnClick}
        list={this.state.list}
        handleItemItemDelete={this.handleItemItemDelete}
      />
    )
  }

  handleInputChange(e) {
    const action = getInputChangeAction(e.target.value)
    store.dispatch(action)
  }

  handleBtnClick() {
    if (!this.state.inputValue) {
      alert('请填写内容')
      return
    }
    const action = getAddItemAction(this.state.inputValue)
    store.dispatch(action)
  }

  handleItemItemDelete(index) {
    const action = getDeleteItemAction(index)
    store.dispatch(action)
  }

  handleStoreChange() {
    this.setState(store.getState())
  }
}

export default TodoList
```

### 无状态组件

* 当一个组件之中只有一个 render 函数的时候，就可以用无状态组件来定义它。

* 无状态组件的性能比较高
* UI 组件通常定义为无状态组件

src/TodoListUI.js

```javascript
import React, {Component} from 'react'
import { Input, Button, List } from 'antd'

const TodoListUI = (props) => {
  return (
    <div style={{padding: 10}}>
      <Input 
        value={props.inputValue} 
        onChange={props.handleInputChange}
        placeholder="todo info"
        style={{width: 300, marginRight: 20}}></Input>
      <Button onClick={props.handleBtnClick} type="primary">保存</Button>
      <List
        style={{marginTop: 10, width: 380}}
        bordered
        dataSource={props.list}
        renderItem={(item, index) => (
          <List.Item onClick={() => {props.handleItemItemDelete(index)}}>
            {item}
          </List.Item>
        )}
      />
    </div>
  )
}

export default TodoListUI
```



### 使用 Redux-thunk 中间件，进行 ajax 请求

redux 的一个中间件，可以把异步请求放入 redux 的 action 里边去编写

使用了 redux-thunk 之后：

* action之前必须是一个对象，加载 redux-thunk 之后，就可以是一个函数了
* store 之前必须接受一个对象，加载 redux-thunk 之后，store 就可以接受函数了，而且它还会帮你自动执行以下这个函数



src/store/index.js

```javascript
import { createStore, applyMiddleware, compose } from 'redux'
import thunk from 'redux-thunk'
import reducer from './reducer'

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({

    }) : compose
const enhancer = composeEnhancers(
  applyMiddleware(thunk),
)
const store = createStore(
  reducer,
  enhancer
)

export default store
```



src/store/actionCreators.js

```javascript
// ...
export const getTodoList = () => {
  return (dispatch) => {
    setTimeout(() => {
      const data = [ "hello", "lv", "jia", "wen"]
      const action = initListAction(data)
      dispatch(action)
    }, 200)
  }
}
// ...
```

src/TodoList.js

```javascript
// ...
class TodoList extends Component {
  // ...
  componentDidMount() {
    const action = getTodoList()
    store.dispatch(action)
  }
	// ....
}
// ...
```



### 什么是 redux 中间件

介于 action 和 store 之间，对 dispatch 方法的一个封装或者说升级

最原始的dispatch 方法，只能接受一个对象，并且将它传给 store

升级之后，dispatch 可以接受函数作为参数，并且会执行函数，在函数内部决定将什么东西传递给 store

其他中间件：redux-logger、redux-saga