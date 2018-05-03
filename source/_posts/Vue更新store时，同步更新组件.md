---
title: Vue更新store时，同步更新组件
date: 2018-04-27 15:50:45
tags:
  - vue
---

第一次使用store的时候，只是简单的当一个全局变量来使用。
使用的场景也很简单，用来统计小朋友的答题的得分。这种场景不会涉及到一个组件上更新store数据，另一个组件需要同步更新数据。store数据会在进入下一题后在created中会获取到，并显示在页面上，而这并不是同步更新的，所以很简单。

但今天我需要同步更新store；第一种方式：直接把store写到template中，这种方式的确是ok的。然而在复杂的场景下依然不行，比如说如果只是需要展示一个变量的值：score，那么可以直接在template中\{$store.score\}即可，但如果是是一个复杂的对象或不只是一两个$store的值，重复这样写就比较复杂了。

我们需要找一个更方便的方式；第二种方式：通过计算属性computed引用$store的值，然后利用watch监听computed的属性，当$store的值发生变化时，可以通过watch中的方法，同步更新组件中引用$store的值。如下：

```html
// a.vue

<template>
  <p>{{status}}</p>
  <button @click="changeStatus">change status</button>
</template>
<script>
export default {
  name: 'a',
  data () {
    return {
      status: this.$store.status
    }
  },
  methods: {
    changeStatus: function () {
      this.status = !this.status
      this.$store.commit('changeStatus')  // 这里需要自己去定义commit方法
    }
  }
}
</script>
```
当在组件A中更新status时，组件B也会触发更新

```html
// b.vue

<template>
  <p>b.vue</p>
  <p>{{status}}</p>
</template>
<script>
export default {
  name: 'b',
  data () {
    return {
      status: this.$store.status
    }
  },
  computed: {
    comStatus: function () {
      return this.$store.status
    }
  },
  watch: {
    comStatus: function (sta) {
      this.status = sta
    }
  }
}
</script>
```
如此，当有比较复杂的数据需要利用store同步更新时，上面的方式应该是比较简便的了。或许还有更方便的方法，还需高人指点。