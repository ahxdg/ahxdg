---
title: input上传图片的问题总结
date: 2017-07-18 10:48:14
tags:
  - 笔记
---

> 这是一个由维护公司老系统引发的问题，上传图片。

下面直接进入主题

##### 问题一：PC浏览器卡顿问题
有些时候我们会发现，chrome浏览器上传图片时会出现卡顿，图片选择的弹窗会延迟很久才弹出来。通过审查元素，我们可以发现，此时的input控件上accept属性有值（如：&lt;input type="file" accept="image/*" /&gt;,或者&lt;input type="file" accept="image/jpg,image/png" /&gt;等等...）。
当我们不设置accept或不给accept设置值时（如：&lt;input type="file" accept="" /&gt;），就不会有卡顿问题了。

**问题分析：**可能是由于设置的接收类型，chrome浏览器在选择文件弹窗时，过滤文件的处理较慢导致弹窗延迟。所以可以针对chrome浏览器特别把accept的值设置为空即可。
```js
// acceptType 控件接收类型， inputDom为控件dom对象
acceptType = isChrome ? "" : "image/*";
inputDom.setAttribute('accept',acceptType);
```

##### 问题二：手机浏览器上传图片时，不能选取图片（图片灰色）
有些同学在开发移动端页面时，会遇到这个问题。明明能打开手机的图片选择界面，但是图片都是灰色的，无法被选择。可能有些同学会发现，可以在界面中选择相册，相册里面的图片是可以被选择的。这个问题想想和chrome浏览器卡顿有点相似的，都是设置image/jpg,...类似的详细属性导致的。
那么，我们把accept的值设置成"image/*"就可以了。


##### 问题三：手机浏览器上调取摄像头问题
这个问题应该是上面那个问题的延伸，在我们把accept属性设置为image/jpg,...等类型时，手机是无法调用摄像头的。而当我们把accept设置成"image/\*" 时就可以了，但是有些同学可能发现有个h5的属性capture【捕捉】，当&lt;input type="file" accept="image/\*" capture="camera" /&gt;时，页面也是可以调用摄像头的。是的，但是在ios上面，会直接触发调用摄像头，而无法选择图库。所以这里需要注意下。如果你仅仅是想调用摄像头，那么在ios上你就可以这么干了，但是安卓上你就要另外用别的方式了。这里说下h5的getMedia方法是可以调用摄像头的。