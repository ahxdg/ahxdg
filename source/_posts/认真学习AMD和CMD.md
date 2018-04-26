---
title: 认真学习AMD和CMD
date: 2018-04-23 13:46:26
tags:
  - js
---

> 一直都觉得AMD和CMD是很厉害的东西，想学习来着，却一直没认认真真的学习过，今天就好好的学习一下。做个记录...


先摘写两句话：
AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。
CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。


那它们是来干嘛的呢？

这个我们可以简单聊一下前端的开发模式：
对于一个小的项目而言：可能一两个人就够了，而前端的开发文件也不多。
但是当一个项目足够大的时候：需要多个人维护和开发，并且有很多模块，如果还是按照上面的开发模式，那将是一场灾难。那么就需要每个人负责一块或者多块内容，也就需要所谓的“模块化”了。

**简单的模块：**
```js
function fn () {
  var name = 'fn';
  var say = function () {
    console.log('hello ' + name);
  }
  // ...
}

fn.say();

需要注意的是：这里fn的name是可以被修改的。

```

**立即执行函数：**
```js
var m = (function () {
  var name = 'fn';
  var say = function () {
    console.log('hello ' + name);
  };
  return {say: say};
  //...
})();

m.say();

这里的name是不能被修改的。

```

然而，这样还是会有问题的。
当小A在一个文件中定义的一个全局的sayHello，而小B在另一个文件中也定义了一个全局的sayHello，那就会有冲突了。

为了避免：
* 命名冲突
* 全局变量冗杂
* 依赖关系的复杂性
* 便于开发等..

我们就需要有一种能满足上面的要求的规范和方法。

**CommonJS**
2009年，美国程序员Ryan Dahl创造了node.js项目，将javascript语言用于服务器端编程。而这也标志着Javascript模块化编程的诞生。
大牛云集的CommonJs社区发力，制定了Modules/1.0（http://wiki.commonjs.org/wiki/Modules/1.0）规范：
1. 模块的标识应遵循的规则（书写规范）
2. 定义全局函数require，通过传入模块标识来引入其他模块，执行的结果即为别的模块暴漏出来的API
3. 如果被require函数引入的模块中也包含依赖，那么依次加载这些依赖
4. 如果引入模块失败，那么require函数应该报一个异常
5. 模块通过变量exports来向往暴漏API，exports只能是一个对象，暴漏的API须作为此对象的属性。

此规范一出，立刻产生了良好的效果，由于其简单而直接，在nodejs中，这种模块化方案立刻被推广开了。

示例如下：
```js
// a.js
exports.add = function (x, y) {
  return x + y;
}
```
```js
// b.js
var a
```


*《--待续--》*