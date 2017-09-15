---
title: '[我是搬运工]-js数组打乱-shuffle'
date: 2017-08-28 08:53:50
tags:
	- 我是搬运工
---

最近在项目要用到一个函数，把数组打乱，结果竟然发现：js没有这么一个函数！！！震惊！！！

然而旁边的陈同学却告诉我php里都内置了这样的函数shuffle，果然php是世界上最NB的语言~

下面摘抄stackoverflow上面的一些精髓的方法

方法一：(852赞)
```js
function shuffle(array) {
  var currentIndex = array.length, temporaryValue, randomIndex;

  // While there remain elements to shuffle...
  while (0 !== currentIndex) {

    // Pick a remaining element...
    randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex -= 1;

    // And swap it with the current element.
    temporaryValue = array[currentIndex];
    array[currentIndex] = array[randomIndex];
    array[randomIndex] = temporaryValue;
  }

  return array;
}

// Used like so
var arr = [2, 11, 37, 42];
arr = shuffle(arr);
console.log(arr);
```
方法二：(347赞)
```js
function shuffleArray(array) {
    for (var i = array.length - 1; i > 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        var temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    return array;
}
```

[传送门](https://stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array)
