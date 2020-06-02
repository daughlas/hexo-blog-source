---
title: vue 性能优化
date: 
categories:
- 前端框架
tags:
- vue
---


## 数据定义

* 不需要把所有的数据都定义在 data 上，可以直接挂载在实例上

## 预渲染

* 由于基于 Vue.js 的单页应用是由 Vue.js 渲染页面，所以页面下载后到渲染会有一定的白屏时间

* 我们可以不依赖 JS，在页面下载后先渲染一张 Loading 图片或者骨架屏结构。
* 当主页面渲染时，把这张占位图片或者结构隐藏

```html
<div id="pre-start">
	loading...
</div>
<div id="app"></div>
```

```javascript
mounted() {
	let preStart = document.querySelector('#pre-start')
	preStart.style.display = 'none'
}
```

## 按需加载

对于首次渲染，我们只需要加载首屏渲染所需要的资源，其他资源可以异步按需加载。这样可以**减少首屏加载的资源包大小**，加快渲染

### 异步组件

```javascript
Vue.component('chart', (resolve) => {
	Promise.all([
		import ('vue-charts/compontents/Echarts.vue'),
		import('echarts/lib/chart/line'),
		import('echarts/lib/component/tooltip'),
		import('echarts/lib/component/legend')
	]).then((args) => {
		const ECharts = args[0]
		ECharts.default.registerTheme('orange', orange)
		resolve(ECharts)
	})
})
```



### 异步路由

工厂函数

import 返回 promise

```javascript
const Recommend = () => import('components/recommend/recommend')
const Singer = () => import('components/singer/singer')
const Rank = () => import('components/rank/rank')
```

## 后编译

### 编译代码冗余？

依赖包本身不编译，它的编译交给应用来做

通过修改 webpack 配置 rules 中的 include

```javascript
module: {
  rules: [
    {loader: 'eslint-loader'...},
    {loader: 'vue-loader'...},
    {
      test: /\.js$/,
      loader: 'babel-loader',
      include: allSource.concat(resolve('node_modules/lodash-es'))
    }
  ]
}
```

### 后编译依赖嵌套？

webpack-post-compile-plugin

需要后编译的依赖包在 package.json 中声明

```javascript
"postCompile": true
```

### 优点

* 一份编译代码
* 一份 ployfill
* npm 包无需编译发布
* 主题定制
* rem 布局



## 运行时优化

### 使用v-if代替v-show

两者的区别是：v-if不渲染DOM，v-show会预渲染DOM

`除以下情况使用v-show`，其他情况尽量使用v-if

- 有预渲染需求
- 需要频繁切换显示状态

### v-for必须加上key，并避免同时使用v-if

一般我们在两种常见的情况下会倾向于这样做:

- 为了过滤一个列表中的项目 比如 `v-for="user in users" v-if="user.isActive"`。在这种情形下，请将 `users`替换为一个计算属性 (比如`activeUsers`)，让其返回过滤后的列表
- 为了避免渲染本应该被隐藏的列表 比如 `v-for="user in users" v-if="shouldShowUsers"`。这种情形下，请将 `v-if` 移动至容器元素上 (比如 ul, ol)

### 事件及时销毁

```javascript
created() {
  addEventListener('touchmove', this.touchmove, false)
},
beforeDestroy() {
  removeEventListener('touchmove', this.touchmove, false)
}
```

## 首屏优化

### 图片裁剪、使用webp

```javascript
Vue.use(VueLazyload, {
  error: require('./assets/img/defaultpic_small.png'),
  filter: {
    webp (listener: any, options: any) {
      if (!options.supportWebp) return
      // listener.src += '.webp'
    }
  }
});
```

### 资源提前请求

vue 中各文件的加载顺序为：router.js => main.js => App.vue => [page].vue => [component].vue

可以在 router 加载的时候去请求数据

```javascript
import Router from 'vue-router'
import store from './store'

store.dispatch('initAjax')
```

### 异步路由

```javascript
{
  path: '/order',
  component: () => import ('./views/order.vue')
}
```

异步组件

```javascript
<template>
  <div>
    <HellowWorld v-if="showHello" />
  </div>
</template>
<script>
export default {
  components: { HellowWorld: () => import('../components/HelloWorld.vue') },
  data() {
    return {
      showHello: false
    }
  },
  methods: {
    initAsync() {
      addEventListener('scroll', (e) => {
        if (scrollY > 100) {
          this.showHello = true
        }
      })
    }
  }
}
</script>
```

### 使用轻量级插件、异步插件

使用webpack-bundle-analyzer查看项目所有包的体积大小，较大的插件包尽量寻找轻量级的替代方案。

首屏用不到的插件、或只在特定场景才会用到的插件使用异步加载（如定位插件，部分情况可以通过URL传递经纬度；或生成画报插件，需要在点击时触发）；插件第一次加载后缓存在本地，使用方式为：

```javascript
// 以定位插件为例
const latitude = getUrlParam('latitude')
const longitude = getUrlParam('longitude')
// 如果没有经纬度参数，则使用定位插件来获取经纬度
if (!latitude || !longitude) {
  // 首次加载定位插件
  // webpack4写法，若使用webpack3及以下，则await import('locationPlugin')即可
  if (!this.WhereAmI) this.WhereAmI = (await import('locationPlugin')).default
  // do sth...
}
```

### 公用cdn

## 网络优化

### 减少网络请求

浏览器对同一时间针对同一域名下的请求有一定数量限制（一般是6个），超过限制数目的请求会被阻塞

### 合理使用preload、dns-prefetch、prefetch

preload具有较高的加载优先级，它可以利用间隙时间预加载资源，将加载和执行分离开，不阻塞渲染和document的onload事件

每次与域名连接都需要进行DNS解析，使用dns-prefetch可以预解析域名的DNS

prefetch会预加载页面将来可能用到的一些资源，优先级较低；对首屏渲染要求较高的项目不建议使用

```html
<head>
  <meta charset="utf-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <link rel="icon" href="/dist/favicon.ico" />
  <!-- dns-prefetch写法 -->
  <link rel="dns-prefetch" href="//www.dpfile.com" />
  <title>md-config</title>
  <!-- preload写法，as属性必须 -->
  <link href="/dist/css/app.52dd885e.css" rel="preload" as="style" />
  <link href="/dist/js/app.05faf3b5.js" rel="preload" as="script" />
  <link href="/dist/js/chunk-vendors.04343b1f.js" rel="preload" as="script" />
  <!-- prefetch写法 -->
  <link href="/dist/js/chunk-vendors.04343b1f.js" rel="prefetch" />
</head>
```

### PWA

PWA支持缓存HTML文档、接口（get）等，降低页面白屏时间 这样即使在弱网甚至断网情况下，也能迅速展示出页面