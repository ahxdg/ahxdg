---
title: CrossDomain
date: 2017-07-28 15:28:02
tags:
	- 笔记
---

> 今天有空，研究下跨域。

##### 什么是跨域？

A resource makes a cross-origin HTTP request when it requests a resource from a different domain, protocol, or port to its own. 
当从不同的域名、协议、端口向自身请求资源时，资源会产生跨源HTTP请求。即为跨域

例如：在http://domain-a.com上的一个页面请求http://domain-b.com/image.jpg，而如今许多页面都会有类似的请求，如：css/js/images

为了安全考虑，浏览器都会遵循同源策略，因此web应用只会将http请求发到自己的域。而为了改善web应用，开发人员要求浏览器允许跨域请求。

为什么说要求浏览器允许跨域请求，因为服务器本身是允许跨域请求的，只是浏览器有同源策略，当我们发起跨域请求时，浏览器会发送请求给别人家的服务器，如果别人家的服务器的返回头里有Access-Control-Allow-Origin:*，就表示允许跨域请求，那么我们才会得到返回的数据，否则，浏览器会自动过滤掉返回的数据，并告诉你跨域请求失败或错误的信息。

##### 跨域方式

1，