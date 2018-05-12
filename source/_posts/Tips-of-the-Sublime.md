---
title: Tips of the Sublime
date: 2017-12-01 16:10:17
tags:
	- Tips
---

> 用sublime也快两年了，知道sublime对于前端开发是很方便的。但自己对于sublime的快捷键和插件等却从没有认真思考和学习过...

这里，暂时简单记录一下，自己平常使用到快捷方式和插件。以便再日后使用时，可以方便查找。


##### 快捷键 (windows)

```avrasm
Ctrl + Shift + D	复制一行
Ctrl  + D		选中该文档中下一个与所有与当前被选中的内容一样的文本
Shift + Tab		向前缩进一个Tab
Shift + Home		选中本行光标之前的所有文本
Shift + End		选中本行光标之后的所有文本
Delete			删除光标所在位置的后一个字符
Ctrl + Enter	在该行后面插入一行，不影响当前行的内容
Ctrl + Shift + Enter	在该行前面插入一行，不影响当前行的内容
Ctrl + ←		光标向左移动一个单词，若是中文，则移至相连中文的第一个字前面
Ctrl + →		光标向后移动一个单词，若是中文，则移至相连中文的第一个字后面
Ctrl + Shift + ↑	向上移动一行
Ctrl + Shift + ↓	向下移动一行

Alt + Shfit + 数字 分屏

Ctrl + Shift + P	打开命令面板
Ctrl + Shift + L	在选中的多行末尾添加光标
Ctrl + P		打开全局查找文件的面板
Ctrl + G		定位到行
Ctrl + R		定位到函数
Ctrl + Shift + F	全局查找

```

注：在win10系統中，当自带输入法为中文时，无法使用Ctrl+Shift+F


##### 插件

###### Emmet
Emmet是一款编辑器插件，可以极大地改善你的HTML和CSS工作流程。

###### jsHint
jsHint js/json语法检查插件，帮你检查js的语法错误，让你的代码规范化。
```
// JSLint.sublime-settings（插件安装的目录下可以找到）
{
	"run_on_save": true		// 保存文件时自动检查
}
```

###### HTML-CSS-JS Prettify
让你的html,js,css,json代码更加优美。

###### Less
高亮显示less文件

###### jade
高亮显示jade

###### LiveReload
结合chrome浏览器，使sublime更改文件后具有自动刷新的功能

###### Vue Syntax Highlight
高亮显示Vue

###### ReactJS
高亮显示React

###### Colorsublime
为sublime提供多种主题

###### Pretty JSON
json文件格式化
