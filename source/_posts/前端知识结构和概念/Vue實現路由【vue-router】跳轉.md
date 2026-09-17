---
title: 路由跳轉
date: 2026-04-20 16:25:05
tags: 路由跳轉
excerpt: vue-router
categories:
  - Vue框架
---
# 項目 導航欄跳轉原理
<mark style="background:#fff192; color:#000; padding:2px 4px; border-radius:3px;">
页面 = 一个个 .vue 组件
跳转 = 路由（Vue Router）
导航栏 = 用 router-link 实现跳转
</mark>

-------------------------
# 实现跳转
<mark style="background:#fff192; color:#000; padding:2px 4px; border-radius:3px;">
項目安装路由：npm install vue-router
</mark>


{% raw %}
```
創建一個導航欄文件夾
裡面放一個導航欄組件
<template>裡面寫文本
<router-link
          v-for="item in navList"
          :key="item.id"
          :to="item.path"
          active-class="active"
          class="nav-item"
        >
          {{ item.name }}
        </router-link>
```

```
<script setup>裡面寫邏輯
import { ref } from 'vue'
const navList = ref([
  { id: 1, name: '', path: '/路徑地址' },
  { id: 2, name: '', path: '/' },
  { id: 3, name: '', path: '/' },
])
```
{% endraw %}

<div style="color:pink">APP組件裡面寫</div>

```
<template>:寫
<router-view /> :實現跳轉
<TopNav />: 導入導航欄組件
```

# <mark style="background:#fff192; color:#000; padding:2px 4px; border-radius:3px;">配置路由</mark>


```
去到 src/ 下面右键 → 新建文件夹 → 命名为 router
现在结构变成：
plaintext
src/
├─ components/
├─ router/      ✅ 你自己新建的
├─ App.vue
└─ main.js
第二步
进入 router/ 文件夹新建文件 → 命名为 index.js
现在结构：
plaintext
src/
├─ router/
│   └─ index.js   ✅ 就是这个
```
<div style="color:pink">複製</div>

```

import { createRouter, createWebHistory } from 'vue-router'

// 引入页面
import xxx from '@/views/xxx.vue'
import xxxx from '@/views/xxx.vue'
import xxxx from '@/views/xxx.vue'

const routes = [
  { path: '/', component: xxxx },
  { path: '/xxx', component: xxxx },
  { path: '/xxxx', component: xxxx },
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router

```
-----------------------------------
<div style="color:pink">你还要创建页面文件夹（views）</div>

```
同样：src/ 下 新建文件夹 views然后在里面创建：
xxx.vue
xxx.vue
xxx.vue
结构：
plaintext
src/
├─ views/
│   ├─ xxx.vue
│   ├─ xxx.vue
│   └─ xxx.vue
```
---------------------------------------------------
<div style="color:pink">最后去 main.js 引入路由</div>

```
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'  // 加这行

const app = createApp(App)
**app.use(router)** // 加这行
app.mount('#app')
```
# 配置新的標籤
打開
`src/router/index.js`
`{ path: '/路徑名', component: 組件名 }`  
<mark style="background:#fff192; color:#000; padding:2px 4px; border-radius:3px;">
path = 網址路徑（定義的）
</mark>

```
（const navList = ref([
  { id: 1, name: '', path: '/xx' },
  { id: 2, name: '', path: '/xxx' },
  { id: 3, name: '', path: '/xxx' },
  { id: 4, name: "", path:"/xxx" }
])）
```

<mark style="background:#fff192; color:#000; padding:2px 4px; border-radius:3px;">
component = 你要顯示哪個頁面（.vue 文件）
</mark>


<div style="color:pink">記得導入</div>

`import Home from '@/views/Home.vue'`

# 總結

<div style="color:pink">APP組件寫</div>

```
<template>
  <!-- 【全站通用，所有頁面都顯示】導航欄 -->
  <TopNav />

  <!-- 【動態坑位】切換頁面就自動換內容 -->
  <router-view />

  <!-- 以後你加底部組件也放這裡，所有頁面都顯示 -->
</template>

<script setup>
// 導入導航欄組件
import TopNav from "@/components/layout/TopNav.vue"
</script>
```

<div style="color:pink">views文件寫標籤欄對應的組件</div>

```
XXXX.vue
xxxx.vue
```

<div style="color:pink">router(路由文件)寫</div>

```
import { createRouter, createWebHistory } from 'vue-router'
// 引入所有頁面
import Home from '@/views/Home.vue'
import xxx from '@/views/xxx.vue'
import xxxxx from '@/views/xxxx.vue'
import xxxx from '@/views/xxx.vue'
// 路徑對應頁面規則
const routes = [
  { path: '/', component: Home },
  { path: '/xxx', component: xxxx },
  { path: '/xxxx', component: xxxx },
  { path: '/xxxx', component: xxxx },
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```
<div style="color:pink">導航欄組件</div>

```
<template>
        <!-- Vue 專用跳轉標籤 router-link -->
        <router-link
          v-for="item in navList"
          :key="item.id"
          :to="item.path"
          active-class="active"
          class="nav-item"
        >
          {{ item.name }}
        </router-link>
</template>

<script setup>
import { ref } from 'vue'
// 菜單數據：名稱 + 跳轉路徑
const navList = ref([
  { id: 1, name: 'Home', path: '/' },
  { id: 2, name: 'xxx', path: '/xxxx' },
  { id: 3, name: 'xxx', path: '/xxx' },
  { id: 4, name: "xxxx", path:"/xxx" }
])
</script>


```








