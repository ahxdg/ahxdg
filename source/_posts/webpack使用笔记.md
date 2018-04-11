---
title: webpack使用笔记
date: 2017-06-28 13:33:31
tags:
	- 笔记
---

webpack十分强大。

##### 安装
```
npm install webpack --save-dev （安装到项目依赖中）
npm install webpack -g （全局安装）
```

##### 配置
使用webpack，需要在项目的根目录下创建一个webpack.config.js文件， 在里面配置一些基本信息：
```js
入口：
entry: './src/index.js'

输入：
output: {
	path: './dist',
	filename: bundle.js
}

模块：
module: {
	rules: [
		{
			test: '',
			use: []
		}
	]
}

扩展：
plugins: [
	new ImageminPlugin({
		/* 配置信息 */
	})
]


```


模块化，移植性好（解耦）