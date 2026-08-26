---
title: Vue3里APP.vue裡面組件的子組件不顯示方法
date: 2026-05-04 11:00:29
tags:
excerpt: Vue3里APP.vue裡面組件的子組件不顯示方法
categories:
    - Vue框架
---
# 方法一

這是一個非常經典的 Vue 架構問題。在 App.vue 放置組件，就像是在房子的地基上放了一個噴水池，無論你走進客廳（首頁）還是臥室（關於頁），那個噴水池永遠都在你的視野裡。

如果你希望某些頁面顯示導航欄，而某些頁面（如：登入頁、全螢幕活動頁）不顯示，通常有以下三種主流解決方案：

1. 使用路由元資訊 (Router Meta) —— 最推薦
這是最優雅且最常用的做法。你可以在路由配置中，幫需要隱藏導航欄的頁面打上一個「標籤」。

```

第一步：在 router/index.js 配置 meta

JavaScript
const routes = [
  {
    path: '/login',
    component: Login,
    meta: { hideNav: true } // 標記這個頁面不需要導航欄
  },
  {
    path: '/',
    component: Home,
    // 沒寫 meta 的預設會顯示
  }
]
第二步：在 App.vue 使用 v-if 判斷
利用 useRoute 來監聽當前路由的標籤。

代码段
<template>
  <!-- 只有當 hideNav 不為 true 時才顯示 -->
  <TopNav v-if="!route.meta.hideNav" />
  <router-view />
</template>

<script setup>
import { useRoute } from 'vue-router'
const route = useRoute()
</script>

如果你定義的是 hideNav（隱藏導航）：那麼 true 就是「我要執行隱藏動作」，false 則是「不隱藏（即顯示）」。
這裡的 ! (驚嘆號) 是「反向」的意思
寫了true:為hide
不寫true:為show
```

# 方法二
```
App.vue (只是一個空盒子)
 ├── MainLayout (外殼帶有 TopNav)
 │    └── 子路由 -> 首頁
 │    └── 子路由 -> 關於頁
 └── LoginView (獨立房間，沒有外殼)
```

```
2. 使用版面配置 (Layout) 模式
如果你的專案比較複雜，有多種不同的介面樣式（例如：前台要頂部導航，後台要側邊欄），建議使用 Layout。

建立一個 MainLayout.vue：

代码段
<template>
  <TopNav />
  <router-view /> <!-- 這裡放子路由內容 -->
</template>

在 router/index.js 嵌套路由：
JavaScript
{
  path: '/',
  component: MainLayout, // 使用帶有導航欄的佈局
  children: [
    { path: '', component: Home },
    { path: 'profile', component: Profile }
  ]
},
{
  path: '/login',
  component: Login // 直接顯示，不套用 MainLayout
}

----------------------------------------
對於不需要導航的頁面（例如登入頁），我們甚至不需要建立模板，直接讓它顯示即可。
//詳細步驟
1. 建立裝潢模板 (Layout)
建立一個 layouts/MainLayout.vue，專門給需要導航欄的頁面用：

代码段
<!-- MainLayout.vue -->
<template>
  <TopNav />    <!-- 這裡有導航欄 -->
  <router-view /> <!-- 這裡用來塞子頁面的內容 -->
  <Footer />    <!-- 這裡有頁腳 -->
</template>

對於不需要導航的頁面（例如登入頁），我們甚至不需要建立模板，直接讓它顯示即可。

2. 修改路由配置 (router/index.js)
這是最關鍵的一步。我們要利用 「嵌套路由 (Children)」 的概念，把需要導航的頁面通通塞進 MainLayout 的肚子裡。

JavaScript
const routes = [
  // 第一組：需要導航欄的頁面
  {
    path: '/',
    component: () => import('../layouts/MainLayout.vue'), // 使用模板
    children: [
      {
        path: '', // 路徑為 /
        component: () => import('../views/Home.vue')
      },
      {
        path: 'profile', // 路徑為 /profile
        component: () => import('../views/Profile.vue')
      }
    ]
  },

  // 第二組：不需要導航欄的頁面
  {
    path: '/login',
    component: () => import('../views/Login.vue') // 直接顯示，不套模板
  },
  {
    path: '/full-screen-video',
    component: () => import('../views/Video.vue') // 也不套模板
  }
]
3. 簡化 App.vue
這時候你的 App.vue 就會變得超級乾淨，它只負責把東西渲染出來：

代码段
<!-- App.vue -->
<template>
  <router-view /> <!-- 就像一個空的舞台 -->
</template>
這樣做的好處是什麼？
邏輯清晰：你不需要在 App.vue 寫一大堆 v-if。

靈活性極高：

如果你以後要做「手機版專屬導航」和「電腦版專屬導航」，你只需要建立兩個不同的 Layout。

如果你有一個「後台管理系統」需要側邊欄，你就再建一個 AdminLayout.vue 即可。

效能更好：Vue 只有在切換不同 Layout 時才會重新渲染大結構，切換同模板下的頁面時，導航欄會保持不動。
```

# 方法三

簡單粗暴：直接放進子組件
如果你只有一兩個頁面需要導航欄，那就不要放在 App.vue。

```

做法： 把 <TopNav/> 從 App.vue 刪除。

做法： 手動把它加進 Home.vue、About.vue 等組件的 <template> 頂部。

缺點： 程式碼重複率高，維護起來比較累。
```
