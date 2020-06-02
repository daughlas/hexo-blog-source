---
title: vue-cli3 打包优化
date: 
categories:
- 前端框架
tags:
- vue
- vue-cli
---

# vue-cli3 打包优化
## 代码压缩
```javascript
configureWebpack: config => {
  if (process.env.NODE_ENV === 'production') {
    // 为生产环境修改配置...
    config.plugins.push(
      //生产环境自动删除console
      new UglifyJsPlugin({
        uglifyOptions: {
          compress: {
            warnings: false,
            drop_debugger: true,
            drop_console: true,
          },
        },
        sourceMap: false,
        parallel: true,
      })
    );
  }
}
```

## 防止 cli3 进入页面的时候加载太多请求
```javascript
chainWebpack: config => {
  // 移除 prefetch 插件
  config.plugins.delete('prefetch')
  // 移除 preload 插件
  config.plugins.delete('preload');
}
```

## CDN 引入
```javascript
// vue.config.js
const isProduction = process.env.NODE_ENV === 'production';
const cdn = {
  css: [],
  js: [
    'https://cdn.bootcss.com/vue/2.6.10/vue.runtime.min.js',
  ]
}
module.exports = {
  chainWebpack: config => {
    // 生产环境配置
    if (isProduction) {
      // 生产环境注入cdn
      config.plugin('html')
        .tap(args => {
          args[0].cdn = cdn;
          return args;
        });
    }
  },
  configureWebpack: config => {
    if (isProduction) {
      // 用cdn方式引入
      config.externals = {
          'vue': 'Vue',
      }
    }
  }
}
```

html 文件也要修改

```javascript
 <!-- 使用CDN的JS文件 -->
<% for (var i in htmlWebpackPlugin.options.cdn && htmlWebpackPlugin.options.cdn.js) { %>
  <link href="<%= htmlWebpackPlugin.options.cdn.js[i] %>" rel="preload" as="script">
<% } %>
```

## 关闭生产环境 sourceMap
```javascript
module.exports = {
  configureWebpack: config => {

  }
}
```


## 配置别名
```javascript
module.exports = {
  configureWebpack: config => {
    if (isProduction) {
      config.resolve.alias
        .set("@", resolve("src"))
        .set("@img", resolve("src/assets/images"))
    }
  }
}
```


## 压缩代码
```javascript
module.exports = {
  chainWebpack: config => {
    if (isProduction) {
      // 压缩代码
      config.optimization.minimize(true);
    }
  }
}
```


## 关闭生产环境 sourceMap
```javascript
module.exports = {
  configureWebpack: config => {
    
  }
}
```


## 关闭生产环境 sourceMap
```javascript
module.exports = {
  configureWebpack: config => {
    
  }
}
```