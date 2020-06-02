---
title: vuex
date: 
categories:
- 前端框架
tags:
- vue
---


![image-20190815144135480](/images/image-20190815144135480.png)

```javascript
import App from 'components/home/App'
import store from 'vuex/editor/store'

// 在 Vue 实例的初始化中声明 store。
new Vue({
  el: 'body',
  components: {
    App
  },
  store
})
```



```bash
└── editor
    ├── mutation-types.js
    ├── actions
    │   └── index.js
    ├── mutations
    │   └── index.js
    ├── plugins
    │   └── index.js
    ├── state
    │   └── index.js
    └── store
        └── index.js

```



editor/store/index.js

```javascript
// vuex/editor/store/index.js
import Vuex from 'vuex'
import state from 'vuex/editor/state'
import mutations from 'vuex/editor/mutations'
import { actionLogPlugin } from 'vuex/editor/plugins'

const store = new Vuex.Store({
  state,
  mutations,
  plugins: [actionLogPlugin]
})

export default store
```

editor/state/index.js

```javascript
// 编辑器相关状态
const editor = {
  ...
}

// 页面相关状态

let page = {
  ...
}

const state = {
  editor,
  page
}

export default state
```



state 中存储了 **editor**和 **page**两个对象，用于存储不同模块的状态。需要说明的是，这里完全可以使用模块机制将其拆开，在 editor.js 里存储编辑器相关的 state 和 mutations，在 page.js 中存储页面相关的 state 和 mutations，以使结构更加清晰。不过这里没有使用模块机制，由于模块数量并不多，也是完全可以接受的。



最佳实践：在子组件中通过 `vuex.getters`来获取该组件需要用到的所有状态：

```javascript
// src/components/h5/Navbar.vue

...
export default {
    data () {
      return {
        ...
      }
    },
    methods: {
      ...
    },
    vuex: {
      actions: {
        ...
      },
      getters: {
        editor(state) {
          return state.editor
        },
        page(state) {
          return state.page
        },
        ...
      }
    }
}
```



editor/mutations/index.js 

```javascript
import * as types from '../mutation-types'

const mutations = {
  [types.CHANGE_LAYER_ZINDEX] (state, dir, index) {
    ...
  },
  [types.DEL_LAYER] (state, index) {
    ...
  },
  [types.REMOVE_FROM_ARR] (state, arr, itemToRemove) {
    ...
  },
  [types.ADD_TO_ARR] (state, arr, itemToAdd) {
    ...
  },
  [types.DEL_SCENE] (state, index) {
    ...
  },
  ...
}

export default mutations
```

 mutation-types.js。该文件主要作用为放置所有的命名 Mutations 的常量，方便合作开发人员厘清整个 app 包含的 mutations。

```javascript
export const CHANGE_LAYER_ZINDEX = 'CHANGE_LAYER_ZINDEX'
export const DEL_LAYER = 'DEL_LAYER'
```





editor/actions/index.js

```javascript
import * as types from '../mutation-types'

export function delLayer( { dispatch }, index) {
  dispatch(types.DEL_LAYER, index)
}

export function delScene( { dispatch }, index) {
  dispatch(types.DEL_SCENE, index)
}

export function removeFromArr( { dispatch }, arr, itemToRemove) {
  dispatch(types.REMOVE_FROM_ARR, arr, itemToRemove)
}

export function addToArr( { dispatch }, arr, itemToAdd) {
  dispatch(types.ADD_TO_ARR, arr, itemToAdd)
}
```



actions 的主要工作就是 **dispatch**（中文译为分发）mutations。初入门的同学可能觉得这是**多此一举**，actions 这一步看起来完全可以省略。

事实上，actions 的出现是为了弥补 mutations 无法实现异步操作的缺陷。所有的异步操作都可以放在 actions 中，比如如果想在调用 delScene 函数 5 秒后再分发 mutations，可以写成这样：

```javascript
function delScene ({ dispatch }, index) {
  setTimeout(() => {
    dispatch(types.DEL_SCENE, index)
  }, 5000)
}
```



引入 actions 的最佳实践

```javascript
import { 
  undoAction, 
  redoAction,
  togglePreviewStatus,
  ...
} from 'vuex/editor/actions'

export default {
    data () {
      return {
        ...
      }
    },
    methods: {
      ...
    },
    vuex: {
      actions: {
        undoAction,
        redoAction,
        togglePreviewStatus,
        ...
      },
      getters: {
        ...
      }
    }
}
```



editor/plugins/index.js

```javascript
...
export function actionLogPlugin(store) {

  store.subscribe((mutation, state) => {

    // 每次 mutation 之后调用
    // mutation 的格式为 { type, payload }
    ...
  })
}
```

核心部分在于采用 `store.subscribe`注册了一个函数。

> 该函数会在每次 mutation 之后被调用。这里 actionLogPlugin 函数完成的是记录每次 mutation 操作，实现撤销重做功能。具体实现逻辑此处不作赘述。

后续我们也会深入地给大家分享 vuex 应用相关的内容









vue 没有编译器的版本，不能写 template



state 状态、数据

mutations 更改状态的函数

actions 异步操作

store 包含以上概念的容易



commit mutations

提交更改，能改state 的只有 mutation

dispatch action 派发动作



commit 调用  mutations 纯同步的

dispatch 调用 actions 可以是异步的