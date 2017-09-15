---
title: vue使用笔记
date: 2017-07-31 14:41:00
tags:
  - 笔记
	- vue
---

> 既然公司有同事开始使用Vue，为了表示不落后，我也要开始vue的探索之旅啦。

##### 技术栈
node + vue + less

##### 开发环境
window + sublime

##### 项目搭建

* 新建项目文档
```
mkdir 项目名称(这里使用pro-one代替)
cd pro-one
```
* 安装vue-cli脚手架 (没有node环境要在电脑上安装node，参考链接：[http://nodejs.cn/](http://nodejs.cn/))
```
npm install --save-dev vue-cli
```
* 创建项目
```
vue init webpack ./ 
// 这里会提示你目标目录已存在，是否确认继续。（目标目录即为当前目录） 输入yes即可。
接下来会有一些确认信息：

 ? Project name (./) ******
 ? Project name ******
 ? Project description (A Vue.js project)
 ? Project description A Vue.js project
 ? Author ******
 ? Author ******
 ? Vue build standalone
 ? Install vue-router? (Y/n)
 ? Install vue-router? Yes
 ? Use ESLint to lint your code? (Y/n)
 ? Use ESLint to lint your code? Yes
 ? Pick an ESLint preset (Use arrow keys)
 ? Pick an ESLint preset Standard
 ? Setup unit tests with Karma + Mocha? (Y/n)
 ? Setup unit tests with Karma + Mocha? Yes
 ? Setup e2e tests with Nightwatch? (Y/n)
 ? Setup e2e tests with Nightwatch? Yes

```
* 初始化项目信息
```
npm install 
```
* 安装less/less-loader
```
npm install less less-loader --save-dev
```
* 安装jquery库
```
npm install jquery --save-dev
```

* 由于此项目为移动端，所以需要修改根目录下的index.html文件
```html
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="utf-8">
+     <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
      <title>smile-junior</title>
    </head>
    <body>
      <div id="app"></div>
      <!-- built files will be auto injected -->
    </body>
  </html>

+ 为新增的行
```
* 在入口文件main.js内挂载全局的jquery，挂载后可以通过this.$使用，但不能用在()=>{}箭头函数中，因为箭头函数中的this指向不是Vue
```js
main.js

  // The Vue build version to load with the `import` command
  // (runtime-only or standalone) has been set in webpack.base.conf with an alias.
  import Vue from 'vue'
  import App from './App'
  import router from './router'

  Vue.config.productionTip = false
+ Vue.prototype.$ = require('jquery')

  /* eslint-disable no-new */
  new Vue({
    el: '#app',
    router,
    template: '<App/>',
    components: { App }
  })

+ 为新增的行
```

* 在开发环境中调试
```
npm run dev
```

* 打包项目
```
npm run build
```
成功，在此不放贴图了。暂时写到这里，后面会继续更...
