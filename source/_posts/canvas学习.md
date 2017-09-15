---
title: canvas学习
date: 2017-08-31 14:07:12
tags:
---


#### 起步

index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<link rel="stylesheet" href="./style.css">
</head>
<body>
	<canvas id="canvas" width="600" height="300"></canvas>
	<script src="./main.js"></script>
</body>
</html>
```

main.js
```js
// 创建canvas对象
   var	canvas = document.querySelector('#canvas'),
	ctx = canvas.getContext('2d');

```


#### 命令
```
stroke 		绘制路径边缘
fill		填充路径内部

线
moveTo		把画笔移动到指定地点
lineTo		绘制一条不可见的路径到指定地点

圆
arc		绘制圆弧或者圆

文本
fillText	在指定位置填充指定文本
strokeText	在指定位置绘制文本边框
measureText	在文本被绘制前，获取文本的宽度

clearRect	擦除画布

```