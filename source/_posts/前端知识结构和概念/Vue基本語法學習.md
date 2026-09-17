---
title: vue框架基本語法  
date: 2026-04-15 19:53:34
tags:
excerpt: Vue 是一套「以数据为中心」的渐进式前端框架。
categories:
  - Vue框架
---

```
1.Vue 基础指令（v-model / v-if / v-for / @click）
2.数据与方法（data / methods）
3.计算属性 computed
4.侦听器 watch
5.组件 Component（Vue 核心）
6.生命周期
7.Vue 路由 vue-router（页面跳转）
8.状态管理 pinia（全局数据）
9.网络请求 axios（调用接口）
10.项目打包部署
```
{% raw %}
# 基礎頁面
```
<template>
  <!-- 這裡寫畫面 -->
  <div>
    <h1>我在學 Vue 基礎</h1>
  </div>
</template>

<script setup>
// 這裡寫功能
</script>

<style>
/* 這裡寫樣式 */
</style>
```

{% raw %}
# ref
## import { ref } from 'vue' 
<div style="color:pink">從vue裡面引入這個{ref工具}响应式变量</div>

```
<template><!--文字-->
  <div>
    <h1>{{ name }}</h1><!--{{ name }} 【核心！插值表達式】-->
  </div>
</template>

<script setup>  //邏輯
import { ref } from 'vue' //從vue裡面引入這個{ref工具}响应式变量
const name = ref('我是 Vue 新手') //定義name是"""
</script>

<style>
h1{
    color:pink;
}
</style> //樣式

```
{% endraw %}

# @click=""
```
<template>
  <div>
    <h1>{{ msg }}</h1>
    <!-- 点击切换 -->
    <button @click="change">点我切换</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// 初始文字
const msg = ref('你还没点我')

// 点击切换函数
function change() {
  // 判断现在的文字是什么
  if(msg.value === '你还没点我'){
    // 第一次点：改成新文字
    msg.value = '你点到我啦！'
  }else{
    // 再点第二次：变回原来的文字
    msg.value = '你还没点我'
  }
}
</script>

<style>
/* 现在页面完全干净，你自己随便写样式 */
</style>
```

<div style="color:pink">三元運算符</div>

```
<template>
  <div>
    <h1>{{ msg }}</h1>
    <button @click="change">点我切换</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const msg = ref('初始文字')
// 提前存好两个要切换的内容
const text1 = '初始文字'
const text2 = '点击后的文字'

function change() {
  // 三元运算符，一句话完成来回切换
     先判断：现在的内容是不是 text1
     如果是真的（是的） → 就变成 text2
     如果是假的（不是） → 就变回 text1

  msg.value = msg.value === text1 ? text2 : text1
}
</script>
```
# v-if=""、v-else
```
<template>
  <div>
    <!-- 判断 isShow 为 true 显示，false 直接删掉标签 -->
    <h1 v-if="isShow">你媽</h1>
    <h1 v-else>你爸</h1>

    <button @click="toggle">点击切换显示隐藏</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const isShow = ref(true) // 默认显示

function toggle() {
  // 三元运算符来回切换 true / false
  isShow.value = isShow.value ? false : true
}
</script>
```
#  v-for="" :key="" 循环渲染
```
<template>
  <div>
    <!-- 循环数组 list，每一项用 item 接收 -->
    <div v-for="item in list" :key="item">
      {{ item }}
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// 数组数据
const list = ref([
  '第一项',
  '第二项',
  '第三项'
])
</script>
```
#  v-model 双向绑定（输入框）v-model=""
```
<template>
  <div>
    <!-- 输入框和 msg 绑定 -->
    <input v-model="msg" placeholder="请输入文字">

    <p>你输入的内容：{{ msg }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const msg = ref('')
</script>s
```
# :class 动态绑定类名（点击切换样式）:style="{ CSS属性 : 你的变量 }"
```
<template>
  <div>
    <p :style="{ color: colorText }">测试文字</p><!--:style="{ CSS属性 : 你的变量 }"-->>
    <button @click="change">点击变色</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const colorText = ref('black')//響應為black

function change() {
  colorText.value = colorText.value === 'black' ? 'blue' : 'black'
}
</script>
```
# 事件参数 / $event 原生事件对象（有點抽象）
```
<template>
  <div>
    <!-- 传自定义参数 -->
    <button @click="say(100)">传数字</button>

    <!-- 获取原生事件对象 $event -->
    <button @click="getEvent($event)">点击获取事件</button><!--$event參數-->>
  </div>
</template>

<script setup>
import { ref } from 'vue'
//終端響應
function say(num) { 
  console.log(num)
}
// 浏览器原生点击事件
function getEvent(e) {
  console.log(e) 
}
</script>
```
eg：
```
<template>
  <div>
    <h2>当前内容：{{ text }}</h2>

    <!-- 点击传不同的文字进去 -->
    <button @click="fn('苹果')">按钮1</button>
    <button @click="fn('香蕉')">按钮2</button>
    <button @click="fn('西瓜')">按钮3</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const text = ref('')

// 同一个函数！
// str 就是外面传进来的数据
function fn(str) {
  // 把传进来的内容赋值给页面
  text.value = str
}
</script>
```





# 組件
```
src
 └─ components/   ✅ 所有小组件全部放这里！
     ├─ 組件.vue   你写的组件
     ├─ Button.vue
     └─ 其他组件...```
```
## 引入
`import 组件名 from '@/components/组件名.vue'`
```
<template>
  <div>
    <h1>我的首页</h1>

    <!-- 3. 使用组件 → 页面就会显示！ -->
    <組件名 />
  </div>
</template>

<script setup>
// 1. 引入组件
import 組件名 from '@/components/組件名.vue'
</script>

<style>
/* 样式 */
</style>
```



# 實現響應式變化的例子
```
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>我的第一个 Vue 程序</title>
  <!-- 引入 Vue -->
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
  <div id="app">
    <h1>{{ message }}</h1>
    <input v-model="message" placeholder="输入文字试试">
  </div>

  <script>
    // 创建 Vue 应用
    const { createApp } = Vue
    createApp({
      // 数据
      data() {
        return {
          message: "你好，Vue！"
        }
      }
    }).mount('#app') // 挂载到页面
  </script>
</body>
</html>
```
{% endraw %}

