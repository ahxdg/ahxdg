---
title: FlexPaper无法播放跨域的swf文件
date: 2018-04-11 15:33:50
tags:
---

> 今天接到一个问题，公司有个项目使用了flexpaper，做文档展示。但无法正常显示swf文件

其实，如果在本地先做好demo，问题可能就很快被发现的。
但是，由于这个项目三个人参与，且后端代码，前端代码和静态资源（swf文件）分别由三个人负责，所以问题一直没有被发现，因为不清楚到底是谁那里出了问题。

中午接到问题时，我也是一脸懵逼，因为刚进来，根本不知道这个项目。

**处理过程：**

1，先把前端代码拿到手，配置好打开发现只是一个样式demo，并没有flexpaper。索性在本地写一个flexpaper的demo

2，找到后端负责人（因为他在开发这个项目，并且是套的前端代码），拿到他的开发环境，浏览器打开发现只是未显示swf文件，并没有报错。

3，找到负责swf文件的人，从他那里copy了一份。直接拖到chrome浏览器发现是直接下载，又拖到360浏览器，可以正常打开，**排除文件出错**。


由于是参照后端的项目写的demo，代码中引入的FlexPaperViewer.swf是服务器上的文件，而播放的swf是本地的文件，所以报了一个错误：
```js
GET http://192.168.0.180:8078/crossdomain.xml 404 (Not Found)
```

从报错信息中可以得知，是跨域问题。

修改FlexPaperViewer.swf的引入路径，把FlexPaperViewer.swf文件下载到本地，引入本地的FlexPaperViewer.swf试一下，可以正常打开。

**也就是说：当我们引入FlexPaperViewer文件时，对于需要播放的swf文件必须是在同一个域名下，不然就会出现上面的错误。**


当然，除此之外还有别的解决方案：就是上面报错的信息中所提到的crossdomain.xml文件，当FlexPaperViewer文件和需要播放的swf文件在不同域时，我们可以在需要播放的swf文件所在域的根目录下添加一个crossdomain.xml：
```xml
<?xml version="1.0"?>
<cross-domain-policy>
   <allow-access-from domain="FlexPaperViewer文件所在的域" />
</cross-domain-policy>
```
意思是：允许FlexPaperViewer文件所在的域对本域名下的文件进行跨域请求，这样就不会报错了。