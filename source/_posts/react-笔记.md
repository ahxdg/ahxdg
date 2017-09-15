---
title: 'react[笔记]'
date: 2017-07-03 15:08:34
tags:
---

> react很流行，学习一下。

##### 技术栈

npm + react + express + ES6 + webpack + ...

##### 项目搭建
```js
1，新建目录
mkdir react

2，进入目录
cd react

3，初始化项目
npm init

4，创建入口文件（vi 是linux下面的命令，window下可手动创建）
vi app.js

/* app.js */
var express = require('express');
var path = require('path');
var app = express();

app.get('/',function(req,res){
	res.send('hello react');
});
app.listen(8899,function(){
	console.log('this project running at port: ' + 8899);
});

5，安装所需模块 - express
npm install dexpress --save-dev

6，运行（测试）
npm start

这里需要先修改package.json文件
"scripts": {
    "start": "node app.js",
},
```
<!-- ![](http://os6p5lsoh.bkt.clouddn.com/art-20170703001.png?imageView2/2/w/400) -->
接下来，我们继续完善~
```
定一下项目结构：
src/ 		- 源代码
 - components/	- 模块
  - index/	- index模块
disc/ 		- 发布代码
 - images/	- 图片目录
 - js/		- js目录
 - css/		- css目录
 - font/	- font目录
 - template/	- 页面目录
node_modules/ 	- node模块
config/ 	- 配置文件
api/ 		- 接口文件
.gitignore 	- git库忽略配置文件
app.js 		- 入口文件
README.md 	- 项目描述
webpack.config.js - webpack配置文件
```

安装所需模块 - webpack
```
npm install --save-dev webpack
```

&lt; - -  先更新到这里，后面继续更~  - - &gt;