---
title: 掘金 vue 组件精讲笔记
date: 
categories:
- 前端框架
tags:
- vue
---
# 掘金 vue 组件精讲笔记

一个再复杂的组件，都是由三部分组成的：prop、event、slot，它们构成了 Vue.js 组件的 API。如果你开发的是一个通用组件，那一定要事先设计好这三部分，因为组件一旦发布，后面再修改 API 就很困难了，使用者都是希望不断新增功能，修复 bug，而不是经常变更接口。如果你阅读别人写的组件，也可以从这三个部分展开，它们可以帮助你快速了解一个组件的所有功能。



`prop` 定义了这个组件有哪些可配置的属性，组件的核心功能也都是它来确定的。写通用组件时，props 最好用**对象**的写法，这样可以针对每个属性设置类型、默认值或自定义校验属性的值



**在组件开发中，最难的环节应当是解耦组件的交互逻辑，尽量把复杂的逻辑分发到不同的子组件中，然后彼此建立联系，在这其中，计算属性（computed）和混合（mixins）是两个重要的技术点，合理利用，就能发挥出 Vue.js 语言的最大特点：把状态（数据）的维护交给 Vue.js 处理，我们只专注在交互上。**



## 组件的通信

### provide / inject

> 这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

主要解决了跨级组件间的通信问题

使用场景：子组件获取上级组件的状态

father.vue

```javascript
export default {
  provide: {
    name: 'Aresn
  }
}
```

son.vue

```javascript
export default {
  inject: ['name'],
  mounted() {
    console.log(this.name)
  }
}
```

>  provide 和 inject 绑定并**不是可响应**的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。



使用 Vuex，最主要的目的是跨组件通信、全局数据维护、多人协同开发。需求比如有：用户的登录信息维护、通知信息维护等全局的状态和数据。

如何 自己实现一个 vuex

app.vue 是整个项目第一个被渲染的组件，而且只会渲染一次（即使切换路由，app.vue 也不会被再次渲染），利用这个特性，很适合做一次性全局的状态数据管理，例如，我们将用户的登录信息保存起来

```javascript
<script>
  export default {
    provide () {
      return {
        app: this
      }
    },
    data () {
      return {
        userInfo: null
      }
    },
    methods: {
      getUserInfo () {
        // 这里通过 ajax 获取用户信息后，赋值给 this.userInfo，以下为伪代码
        $.ajax('/user/info', (data) => {
          this.userInfo = data;
        });
      }
    },
    mounted () {
      this.getUserInfo();
    }
  }
</script>
```



如果你的项目足够复杂，或需要多人协同开发时，在 `app.vue` 里会写非常多的代码，多到结构复杂难以维护。这时可以使用 Vue.js 的混合 `mixins`，将不同的逻辑分开到不同的 js 文件里。

下面是一个 mixin

*user.js：*

```javascript
export default {
  data () {
    return {
      userInfo: null
    }
  },
  methods: {
    getUserInfo () {
      // 这里通过 ajax 获取用户信息后，赋值给 this.userInfo，以下为伪代码
      $.ajax('/user/info', (data) => {
        this.userInfo = data;
      });
    }
  },
  mounted () {
    this.getUserInfo();
  }
}
```

然后在 `app.vue` 中混合：

*app.vue：*

```javascript
<script>
  import mixins_user from '../mixins/user.js';

  export default {
    mixins: [mixins_user],
    data () {
      return {

      }
    }
  }
</script>
```



独立组件使用 provide / inject 的场景，主要是具有联动关系的组件，比如接下来很快会介绍的第一个实战：具有数据校验功能的表单组件 Form。它其实是两个组件，一个是 Form，一个是 FormItem，FormItem 是 Form 的子组件，它会依赖 Form 组件上的一些特性（props），所以就需要得到父组件 Form

古老的做法

```javascript
computed: {
  form () {
    let parent = this.$parent;
    while (parent.$options.name !== 'Form') {
      parent = parent.$parent;
    }
    return parent;
  }
}
```

每个组件都可以设置 `name` 选项，作为组件名的标识，利用这个特点，通过向上遍历，直到找到需要的组件。

问题：

* 父组件向子组件（跨级）传递数据

* 子组件向父组件（跨级）传递数据

### 派发与广播 —— 自行实现 dispatch 和 broadcast 方法

自己监听自己 $emit 的事件

```javascript
<template>
  <div>
    <button @click="handleEmitEvent">触发自定义事件</button>
  </div>
</template>
<script>
  export default {
    methods: {
      handleEmitEvent () {
        // 在当前组件上触发自定义事件 test，并传值
        this.$emit('test', 'Hello Vue.js')
      }
    },
    mounted () {
      // 监听自定义事件 test
      this.$on('test', (text) => {
        window.alert(text);
      });
    }
  }
</script>

```

**自行实现 dispatch 和 broadcast**

```javascript
import Emitter from '../mixins/emitter.js'
export default {
	mixins: [Emitter]
  methods: {
    handleDispatch() {
			this.dispatch()
    }
    handleBroadcast() {
      this.broadcast()
    }
  }
}
```

*emitter.js*

```javascript
function broadcast(componentName, eventname, params) {
	this.$children.forEach(child => {
		cosnt name = child.$options.name
		if (name === componentName) {
      child.$emit.apply(child, [eventName].concat[params])
		} else {
      broadcast.apply(child,[componentName, eventName].concat([params]))
		}
	})
}

export default {
  methods: {
  	dispatch(componentName, eventName, params) {
      let parent = this.$parent || this.$root
      let name = parent.$options.name
      
      while(parent && (!name || name !== componentName)) {
      	parent = parent.$parent
      	if (parent) {
          name = parent.$options.name
      	}
			}
			if (parent) {
        parent.$emit.apply(parent, [eventName].concat(params))
			}
  	},
  	broadcast(componentName, eventName, params) {
      broadcast.call(this, componentName, eventName, params)
  	}
	}
}
```

具体用法

```javascript
<!-- A.vue -->
<template>
	<button @click="handleClick">触发事件</button>
</template>
<script>
  import Emitter from '../mixins/emitter.js';
  
  export default {
    name: 'componentA',
    mixins: [ Emitter ],
    methods: {
      handleClick () {
        this.broadcast('componentB', 'on-message', 'Hello Vue.js');
      }
    }
  }
</script>
```



```javascript
// B.vue
export default {
  name: 'componentB',
  created () {
    this.$on('on-message', this.showMessage);
  },
  methods: {
    showMessage (text) {
      window.alert(text);
    }
  }
}
```



### 第一个实战项目

一个 Vue.js 组件的接口来自三个部分：props、slots、events。**



注意，Vue.js 的组件渲染顺序是由内而外的，所以 FormItem 要先于 Form 渲染，在 FormItem 的 mounted 触发时，我们向 Form 派发了事件 `on-form-item-add`，并将当前 FormItem 的实例（this）传递给了 Form，而此时，Form 的 mounted 尚未触发，因为 Form 在最外层，如果在 Form 的 mounted 里监听事件，是不可以的，所以要在其 created 内监听自定义事件，Form 的 created 要先于 FormItem 的 mounted。



Form 支持两种事件来触发校验：

- **blur**：失去焦点时触发，常见的有输入框失去焦点时触发校验；
- **change**：实时输入时触发或选择时触发，常见的有输入框实时输入时触发校验、下拉选择器选择项目时触发校验等。



### 组件的通信 3: 找到任意组件实例 —— findComponents 系列方法

**src/utils/assist.js**


### Vue 的构造器 extend 与手动挂载 $mount

创建一个 Vue 实例时，都会有一个选项 `el`，来指定实例的根节点，如果不写 `el` 选项，那组件就处于未挂载状态。

`Vue.extend` 的作用，就是基于 Vue 构造器，创建一个“子类”，它的参数跟 `new Vue` 的基本一样，但 `data` 要跟组件一样，是个函数，再配合 `$mount` ，就可以让组件渲染，并且挂载到任意指定的节点上，比如 body。

```javascript
import Vue from 'vue'

const AlertComponent = Vue.extend({
  template: `<div>{{message}}</div>`,
  data() {
    return {
      message: 'Hello, Lvjiawen'
    }
  }
})
```

我们调用 `$mount` 方法对组件进行了手动渲染，但它仅仅是被渲染好了，并没有挂载到节点上，也就显示不了组件。

```javascript
const component = new AlertComponent().$mount()
```

```javascript
document.body.appendChild(component.$el);
```

`$mount` 也有一些快捷的挂载方式，以下两种都是可以的：

```javascript
// 在 $mount 里写参数来指定挂载的节点
new AlertComponent().$mount('#app');
// 不用 $mount，直接在创建实例时指定 el 选项
new AlertComponent({ el: '#app' });
```



实现同样的效果，除了用 extend 外，也可以直接创建 Vue 实例，并且用一个 Render 函数来渲染一个 .vue 文件：

```javascript
import Vue from 'vue'
import Notification from './notification.vue'

const props = {}

const Instance = new Vue({
  render(h) {
  	return h(Notification, {
      props: props
  	})
	}
})

const component = Instance.$mount()
document.body.appdenChild(component.$el)
const notification = Instance.$children[0]
```



需要注意的是，我们是用 `$mount` 手动渲染的组件，如果要销毁，也要用 `$destroy` 来手动销毁实例，必要时，也可以用 `removeChild` 把节点从 DOM 中移除。



### 实战 3

new Function 的语法：

```javascript
new Function ([arg1[, arg2[, ...argN]],] functionBody)
```

arg1, arg2, ... argN 是被函数使用的参数名称，**functionBody** 是一个含有包括函数定义的 JavaScript 语句的**字符串**。也就是说，示例中的字符串 `return a + b` 被当做语句执行了。



notification.js 并不是最终的文件，它只是对 alert.vue 添加了一个方法 `newInstance`。虽然 alert.vue 包含了 template、script、style 三个标签，并不是一个 JS 对象，那怎么能够给它扩展一个方法 `newInstance` 呢？事实上，alert.vue 会被 Webpack 的 vue-loader 编译，把 template 编译为 Render 函数，最终就会成为一个 JS 对象，自然可以对它进行扩展。





### 更灵活的组件： render 函数与 functional render

一般来说，我们写 Vue.js 组件，模板都是写在 `<template>` 内的，但它并不是最终呈现的内容，template 只是一种对开发者友好的语法，能够一眼看到 DOM 节点，容易维护，在 Vue.js 编译阶段，会解析为 Virtual DOM。



与 DOM 操作相比，Virtual DOM 是基于 JavaScript 计算的，所以开销会小很多。



![image-20190325162921254](/Users/lvjiawen/Library/Application Support/typora-user-images/image-20190325162921254.png)



正常的 DOM 节点在 HTML 中是这样的：

```html
<div id="main">
  <p>文本内容</p>
  <p>文本内容</p>
</div>
```

用 Virtual DOM 创建的 JavaScript 对象一般会是这样的：

```javascript
const vNode = {
  tag: 'div',
  attributes: {
    id: 'main'
  },
  children: [
    // p 节点
  ]
}
```

vNode 对象通过一些特定的选项描述了真实的 DOM 结构。



来看一组 template 和 Render 写法的对照：

```html
<template>
  <div id="main" class="container" style="color: red">
    <p v-if="show">内容 1</p>
    <p v-else>内容 2</p>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        show: false
      }
    }
  }
</script>
```



```javascript
export default {
  data() {
    return {
      show: false
    }
  },
  render: (h)=> {
    let childNode
    
    if (this.show) {
      childNode = h('p', '内容1')
    } else {
      childNode = h('p', '内容2')
    }
    
    return h('div', {
      attrs: {
        id: 'main'
      },
      class: {
        container: true
      },
      style: {
        color: 'red
      }
    }, [childNode])
  }
}
```

**这里的 `h`，即 `createElement`，是 Render 函数的核心。**

h 有三个参数，分别是：

1. 要渲染的元素或组件
2. 对应属性的数据对象
3. 子节点



重复渲染多个组件或元素，可以通过一个循环和工厂函数来解决：

```javascript
const Child = {
  render: (h) => {
    return h('p', 'text');
  }
}

export default {
  render: (h) => {
    const children = Array.apply(null, {
      length: 5
    }).map(() => {
      return h(Child);
    });
    return h('div', children);
  }
}
```



在 runtime 版本的 Vue.js 中，如果使用 Vue.extend 手动构造一个实例，使用 template 选项是会报错的



Vue.js 提供了一个 `functional` 的布尔值选项，设置为 true 可以使组件无状态和无实例，也就是没有 data 和 this 上下文。这样用 Render 函数返回虚拟节点可以更容易渲染，因为函数化组件（Functional Render）只是一个函数，渲染开销要小很多。