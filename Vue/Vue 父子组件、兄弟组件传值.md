---
title: Vue 父子组件、兄弟组件传值
date: 2021-3-28
tags:
  - Vue 基础
categories:
  - Vue
---

## 父子组件



- **父组件向子组件传值**



父组件通过 `v-bind` 传值，子组件通过 `props` 接收



```vue
// -----父组件-----
<template>
  <div>
    <h1>父组件</h1>
    <child :sendMessage="fatherMessage"></child>
  </div>
</template>
<script>
import child from '@/components/child'
export default {
  name: 'father',
  components: {
    child
  },
  data() {
    return {
      fatherMessage: 'hello，my son' // 传递给子组件的值
    }
  }
}
</script>
```



```vue
// -----子组件-----
<template>
  <div>
    <h1>子组件</h1>
    <span>获取父组件的值:{{sendMessage}}</span> 
  </div>
</template>
<script>
export default {
  name: 'child',
  data() {
    return {
    }
  },
  props: ['sendMessage'] // 接收父组件的值
}
</script>
```



- **子组件向父组件传值**



子组件通过触发事件 `$emit` 给父组件传值，父组件通过 `v-on` 监听子组件的状态



```vue
// -----子组件-----
<template>
  <div>
    <h1>子组件</h1>
    <button @click="sendToFather">子组件给父组件传值</button>
  </div>
</template>
<script>
export default {
  name: 'child',
  data() {
    return {
      childMessage: 'hello，my father'
    }
  },
  methods: {
    sendToFather: function() {
      // 参数1 getChildValue 作为中间状态，参数2 this.childMsg 即为传递给父组件的数据
      this.$emit('getChildValue', this.childMessage);
    }
  }
}
</script>
```



```vue
// -----父组件-----
<template>
  <div>
    <h1>父组件</h1>
    <!-- 引入子组件 定义一个on的方法监听子组件的状态，然后通过getChild方法获取子组件传递的数据-->
    <child v-on:getChildValue="getChild"></child>
    <span>这是来自子组件的数据：{{childValue}}</span>
  </div>
</template>
<script>
import child from '@/components/child'
export default {
  name: 'father',
  components: {
    child
  },
  data() {
    return {
      childValue: ''
    }
  },
  methods: {
    getChild: function(data) {
      // 此时的参数 data 为子组件传递的值，即 this.$emit() 的第二个参数
      this.childValue = data;
    }
  }
}
</script>
```



## 兄弟组件



通过一个中间桥来进行传值，它承担起了组件之间通信的桥梁，也就是中央事件总线



- **第一种方式：新建文件方式**



1. 新建 bus.js



```js
import Vue from 'vue'
export default  new Vue
```



1. 在需要传值和接受值的 vue 文件中，各自引入 bus.js



1. 使用 `bus.$emit("methodName", data)` 传递数据



```vue
<template>
  <div>
    <p>第一个组件</p>
    <button @click="sendMsg">向第二个组件发送信息</button>
  </div>
</template>
<script>
// 导入中间桥
import bus from '../util/bus'
export default {
  data () {
    return {}
  },
  methods: {
    sendMsg () {
      bus.$emit('firstMsg', 'hello，my partner')
    }
  }
}
</script>
```



1. 使用 `bus.$on("methodName", val =>{ })` 来接收数据



```vue
<template>
  <div>
    <p>第二个组件</p>
    <p>从同级组件传递过来的信息：{{message}}</p>
  </div>
</template>
<script>
// 导入中间桥
import bus from '../tools/center'
export default {
  data () {
    return {
      message: '默认值'
    }
  },
  mounted () {
    bus.$on('firstMsg', function (msg) {
      this.message = msg
    })
  }
}
</script>
```



- **第二种方式：全局事件总线**



1. 在 main.js 中，`new` 一个空的 `vue` 挂在到 `Vue` 原型上



```js
Vue.prototype.$bus = new Vue();
```



1. 使用 `this.$bus.$emit("methodName", data)` 传递数据



```vue
<template>
  <div>
    <p>第一个组件</p>
    <button @click="sendMsg">向第二个组件发送信息</button>
  </div>
</template>
<script>
// 导入中间桥
import bus from '../util/bus'
export default {
  data () {
    return {}
  },
  methods: {
    sendMsg () {
      this.$bus.$emit('firstMsg', 'hello，my partner')
    }
  }
}
</script>
```



1. 使用 `this.$bus.$on("methodName", val =>{ })` 来接收数据



```vue
<template>
  <div>
    <p>第二个组件</p>
    <p>从同级组件传递过来的信息：{{message}}</p>
  </div>
</template>
<script>
// 导入中间桥
import bus from '../tools/center'
export default {
  data () {
    return {
      message: '默认值'
    }
  },
  mounted () {
    this.$bus.$on('firstMsg', function (msg) {
      this.message = msg
    })
  }
}
</script>
```