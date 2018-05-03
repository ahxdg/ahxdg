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
var add = require('a').add;

add(3, 2); // 5
```

**CommonJS规范：**
+ 每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见。
+ 模块必须通过 module.exports 导出对外的变量或接口，通过 require() 来导入其他模块的输出到当前模块作用域中。

由于CommonJS是同步的，所以想要调用模块里的方法，就必须先加载模块。这在服务端是没有问题的，然而在浏览器却行不通。由于浏览器加载资源时是异步加载的，又不能保证网络的稳定性，所以传统的CommonJS模块在浏览器环境中无法正常加载。

怎么办？找方法：
1，开发一个服务端组件，对代码进行静态分析，将模块和它的依赖列表一起返回给客户端。当然这实现起来需要一定的技术方案，在此不做探讨。
2，定义一套规范，用一个标准去约束开发，利用依赖列表和回调函数达到“异步模块定义”，这就是**AMD规范**。

AMD也采用require()语句加载模块，但是不同于CommonJS，它要求两个参数：
```js
require([module], callback);
```
第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数。
上面的b.js改成AMD形式，就是下面这样：
```js
require(['a'], function (mod) {
  mod.add(3, 2)
})
```
目前，只有require.js实现了AMD规范。更多关于[require.js](http://requirejs.org/)可以参考官网。

接下来聊一聊CMD是什么：

**CMD** 是 sea.js 在推广过程中对模块定义的规范化产出。

CMD规范：一个模块就是一个文件。
书写格式如下：
```js
define(factory);
```
define 是一个全局函数，用来定义模块。
define 接受 factory 参数，factory 可以是一个函数，也可以是一个对象或字符串。
factory 为对象、字符串时，表示模块的接口就是该对象、字符串。
factory 为函数时，表示是模块的构造方法。执行该构造方法，可以得到模块向外提供的接口。factory 方法在执行时，默认会传入三个参数：require、exports 和 module：
```js
define(function(require, exports, module) {
  // 模块代码
});
```
AMD和CMD的区别：
AMD是前置依赖，CMD是就近依赖。

至于那种方式更好，在此不做比较和分析，因为这两种技术都将过时，但还是可以去学习和借鉴的。技术在推动世界的进步，善于利用，也是一种技能。