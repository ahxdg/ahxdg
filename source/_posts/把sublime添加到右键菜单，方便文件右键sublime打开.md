---
title: 把sublime添加到右键菜单，方便文件右键sublime打开
date: 2017-11-19 23:47:33
tags:
	- Tips
---

> 最近重新装了系统（目前是win10），又把sublime装了一遍，在写项目时发现文件不支持右键打开，无奈在网上搜了一些教程，学习了一下。并在这里做一下记录，以便日后再次需要时，方便查找。

#### 方法大致有两种：

** 1, 直接修改注册表 **

win+R打开运行窗口，输入regedit打开注册表；找到HKEY_CLASSES_ROOT > * > shell，新建一个项[Edit with Sublime]（名字随意起），在该项下新建一个字符串值[icon]，修改该值的数据为“D:\Program Files\Sublime Text Build 3143\sublime_text.exe,0” ，“,0”前的值为sublime执行文件.exe的路径。
之后我们需要在该项下面再新建一个项[command]，直接修改其默认值的数据为“D:\Program Files\Sublime Text Build 3143\sublime_text.exe %1”即可。

其实如果注册表里HKEY_CLASSES_ROOT > * > shell下面有值的话，你可以参照原有值的方式去添加。

** 第二种方法是把这些流程写成一个文件，运行一下，直接添加到注册表里。我本人并没有去尝试，不过有兴趣的可以尝试一下，关于往注册表中添加命令的教程网上有很多 **