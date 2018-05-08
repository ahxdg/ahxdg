---
title: '[基础] - JS之sort'
date: 2018-05-07 23:19:25
tags:
---

> 虽然干前端很久了，但其实js的基础好多还都没有掌握。呢，在sort上卡住了...

起因：今天写代码，需要用到排序，排数组对象的那种。

就用了js的sort方法，我知道不传参数就是按从小到大排，可我以为传一个比较函数comparer(a,b)
如果return a>b，就是从小到大排；否则就是从大小排。（ 很蠢吧~~ ）

其实是：
```js
arr.sort(comparer)
```
comarer 可选。用来指定按某种顺序进行排列的函数。
如果省略，元素按照转换为的字符串的各个字符的<u>Unicode位点</u>进行排序。
如果没有省略，则会根据函数返回的值进行排序：
大于0，则按从小到大排序；
小于0，则按从大到小排序；
等于0，不处理。
```js
arr = [{a:2,b:2},{a:2,b:1},{a:1,b:2}]

arr.sort(function (x,y) {
  return x.a-y.a
})
// 排序后的arr，后面两项由于比较时相等，便未做处理
// [
//  {a: 1, b: 2},
//  {a: 2, b: 2},
//  {a: 2, b: 1}
// ]
```

并且，sort方法有返回值，返回值为被重新排序后的原数组。

**注意：**
如果没有使用comparer，对数字排序时需要注意，有可能会出现看似错误的排序：
```js
arr = [1,3,10,2]
arr.sort()
// [1, 10, 2, 3]
```
之所以会出现上面的情况，因为在比较时数字被转换成了字符串，而字符串‘1’的位置在‘2’，‘3’的前面

所以，在用sort函数时，可以加一个comparer函数：
```js
function comparer(a, b) {
  if (a < b) {
    return -1;
  }
  if (a > b) {
    return 1;
  }
  return 0;
}
```
如果是对数字比较，可以用下面简便的方式：
```js
function comparer(a, b) {
  return a - b;  // 如果想要大的在前，可以return b - a;
}
```

对象可以按照某个属性排序：(此实例摘自MDN)
```js
var items = [
  { name: 'Edward', value: 21 },
  { name: 'Sharpe', value: 37 },
  { name: 'And', value: 45 },
  { name: 'The', value: -12 },
  { name: 'Magnetic' },
  { name: 'Zeros', value: 37 }
];

// sort by value
items.sort(function (a, b) {
  return (a.value - b.value)
});

// sort by name
items.sort(function(a, b) {
  var nameA = a.name.toUpperCase(); // ignore upper and lowercase
  var nameB = b.name.toUpperCase(); // ignore upper and lowercase
  if (nameA < nameB) {
    return -1;
  }
  if (nameA > nameB) {
    return 1;
  }

  
// names must be equal

  return 0;
});
```

在MDN上有提到，如果comparer函数的比较复杂且元素较多的时候，可以映射改善排序。
也就是说comparer的两个参数a, b要进行一系列的运算后进行比较时，可能会导致很高的负载。其思路时，先对要比较的两个值进行处理，并做好映射关系。然后对处理后的值直接去比较，再跟据映射关系得到一个新的数组。实例如下：
```js
// 需要被排序的数组
var list = ['Delta', 'alpha', 'CHARLIE', 'bravo'];

// 对需要排序的数字和位置的临时存储
var mapped = list.map(function(el, i) {
  return { index: i, value: el.toLowerCase() };
})

// 按照多个值排序数组
mapped.sort(function(a, b) {
  return +(a.value > b.value) || +(a.value === b.value) - 1;
});

// 根据索引得到排序的结果
var result = mapped.map(function(el){
  return list[el.index];
});
```

这种说法，我没有去验证。不过我验证了元素较多的情况，我用了12万条数据，正常的比较耗时为75ms左右；用上面的方法，耗时为200ms左右。之所以这种方法更耗时，原因在于排序前后两次的map反而增加了处理时间，而中的sort方法并没有提高。

所以，上面的方法是否真的可取，还是留给你自己去验证吧。