---
title: vue使用笔记-背景图片的路径问题
date: 2017-08-25 11:23:10
tags:
	- 笔记
	- webpack
	- vue
---

##### 问题：
最近使用vue时，发现打包完的项目中，背景图片不能加载。
打包前background的代码
```css
background: url("../assets/images/audio.png") center no-repeat;
```
打包后background的代码
```css
background: url(/static/img/audio.8d110e4.png) center no-repeat;
```
打包后的文件，由于没有放到网站的根目录下，所以打包后背景图片无法获取。

##### 解决办法：
在网上查了很多，很多答案都没有相关性，也没解决问题。其实很简单，无非就是把相对根目录的路径改成相对css文件的路径即可。
在build/utils.js里的有个ExtractTextPlugin对象，给它添加 publicPath: '../../' 就行了。

这里问题的关键就是