---
title: Pinia+router守卫+axios封装+骨架屏实现loading动画
date: 2026-05-18 10:18:46
tags: loading动画
excerpt: loading动画
categories:
    - Vue框架
---
📌 项目立项书：Vite-WP-Blog
项目名称：Vite-WP-Blog（基于Vue3 + WP）

核心亮点：

全站异步组件延迟加载。

Axios 拦截器自动吞吐 Loading 状态。

Pinia 统一管理文章状态。

页面局部“骨架屏”占位，拒绝死板的全屏转圈。css动画闹嘛了



<div style="color:pink">技术栈：Vue 3 (Setup) + Vite + Vue Router + Pinia + Axios + CSS 动画（或 Element Plus 骨架屏）。</div>

🛠️ 第一阶段：初始化项目与环境准备
现在我们开始第一步：搭架子。请在你的电脑终端（Terminal）执行以下命令：

Bash
# 1. 创建 Vue 3 项目（按提示选择：支持 Router、支持 Pinia）
npm create vue@latest

# 2. 进入项目目录（假设你起名叫 wp-blog）
cd wp-blog

# 3. 安装 axios 依赖
npm install axios
🧱 第二阶段：核心代码编写目录
项目建好后，我们只需要聚焦在 4 个核心文件上。你可以按照下面的结构去创建和编写代码：

## 状态大脑：src/stores/wp.js
职责：封装专用的 Axios，并拦截请求，控制 isLoading 状态。
```
JavaScript
import { defineStore } from 'pinia'
import { ref } from 'vue'
import axios from 'axios'

export const useWpStore = defineStore('wp', () => {
  const posts = ref([])         // 文章列表
  const currentPost = ref(null) // 当前文章详情
  const isLoading = ref(false)  // 全局骨架屏开关

  // 创建 WP 专属请求实例
  const wpApi = axios.create({
    // 💡 提示：换成你自己的 WP 网址，或者先用官方演示地址测试
    baseURL: 'https://techcrunch.com/wp-json/wp/v2' 
  })

  // 核心：拦截器控制开灯、关灯
  wpApi.interceptors.request.use(config => {
    isLoading.value = true
    return config
  })
  wpApi.interceptors.response.use(
    res => { isLoading.value = false; return res.data },
    err => { isLoading.value = false; return Promise.reject(err) }
  )

  // 异步获取文章列表
  const getPosts = async () => {
    posts.value = await wpApi.get('/posts?per_page=6&_embed') // _embed 可以顺便带出特色图片
  }

  // 异步获取单篇文章详情
  const getPostDetail = async (id) => {
    currentPost.value = await wpApi.get(`/posts/${id}`)
  }

  return { posts, currentPost, isLoading, getPosts, getPostDetail }
})
```
##  列表页：src/views/BlogList.vue
职责：利用 v-if 实现骨架屏占位与真实文章的切换。
```
HTML
<template>
  <div class="blog-list">
    <h1>📝 最新文章</h1>

    <!-- 骨架屏占位（数据加载时显示） -->
    <div v-if="wpStore.isLoading" class="skeleton-container">
      <div v-for="i in 3" :key="i" class="skeleton-card">
        <div class="skeleton-title"></div>
        <div class="skeleton-text"></div>
      </div>
    </div>

    <!-- 真实文章列表（数据加载完显示） -->
    <div v-else class="post-grid">
      <article 
        v-for="post in wpStore.posts" 
        :key="post.id" 
        class="post-card"
        @click="goToDetail(post.id)"
      >
        <h2 v-html="post.title.rendered"></h2>
        <div v-html="post.excerpt.rendered" class="excerpt"></div>
      </article>
    </div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useWpStore } from '@/stores/wp'

const wpStore = useWpStore()
const router = useRouter()

onMounted(async () => {
  await wpStore.getPosts() // 触发异步请求
})

const goToDetail = (id) => {
  router.push(`/post/${id}`)
}
</script>

<style scoped>
/* 真实卡片样式 */
.post-card { border: 1px solid #eee; padding: 20px; border-radius: 8px; margin-bottom: 15px; cursor: pointer; transition: 0.3s; }
.post-card:hover { transform: translateY(-3px); box-shadow: 0 4px 12px rgba(0,0,0,0.1); }

/* 骨架屏灰色闪烁占位样式 */
.skeleton-card { background: #f5f5f5; height: 120px; padding: 20px; border-radius: 8px; margin-bottom: 15px; animation: pulse 1.5s infinite; }
.skeleton-title { background: #e0e0e0; height: 24px; width: 40%; margin-bottom: 15px; }
.skeleton-text { background: #e0e0e0; height: 16px; width: 80%; }
@keyframes pulse { 0% { opacity: 0.6; } 50% { opacity: 1; } 100% { opacity: 0.6; } }
</style>
3. 详情页：src/views/BlogDetail.vue
职责：点进去看文章大图和正文，同样自带骨架屏。

HTML
<template>
  <div class="blog-detail">
    <button @click="$router.back()">⬅️ 返回列表</button>

    <!-- 详情页骨架屏 -->
    <div v-if="wpStore.isLoading" class="skeleton-detail">
      <div class="skeleton-title"></div>
      <div class="skeleton-content" v-for="i in 4" :key="i"></div>
    </div>

    <!-- 真实文章正文 -->
    <article v-else-if="wpStore.currentPost">
      <h1 v-html="wpStore.currentPost.title.rendered"></h1>
      <div v-html="wpStore.currentPost.content.rendered" class="content"></div>
    </article>
  </div>
</template>

<script setup>
import { onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { useWpStore } from '@/stores/wp'

const wpStore = useWpStore()
const route = useRoute()

onMounted(async () => {
  // 根据路由传过来的文章 ID 去抓取详情
  await wpStore.getPostDetail(route.params.id) 
})
</script>

<style scoped>
.skeleton-detail { margin-top: 30px; animation: pulse 1.5s infinite; }
.skeleton-title { background: #e0e0e0; height: 40px; width: 60%; margin-bottom: 30px; }
.skeleton-content { background: #e0e0e0; height: 18px; margin-bottom: 12px; }
@keyframes pulse { 0% { opacity: 0.6; } 50% { opacity: 1; } 100% { opacity: 0.6; } }
</style>
4. 路由配置：src/router/index.js
职责：配置成异步路由组件，让打包体积更小。

JavaScript
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'home',
      component: () => import('@/views/BlogList.vue') // 异步组件
    },
    {
      path: '/post/:id',
      name: 'post',
      component: () => import('@/views/BlogDetail.vue') // 异步组件
    }
  ]
})
```

export default router
# 🚀 项目启动！
把代码保存后，在终端运行：

```
Bash
npm run dev
```

# Axios 的拦截器（Interceptors）控制loading

如果没有拦截器，你的项目会变成这样：

写 100 个接口，你就得手动在 100 个地方写代码去加 Token。

后端一旦改了身份验证的方式，你需要苦哈哈地去修改 100 个地方。

每个页面都要自己写 loading = true 和 loading = false。

而有了拦截器，你把这些“与核心业务无关、但每个接口都要做的重复体力活”全部抽离到了一个统一的文件里。页面只负责“要数据”和“渲染数据”，整个项目的架构变得极其干净和标准。

# 前置守卫（beforeEach）：
用户点击跳转，在进入新页面之前，路由守卫立刻通知 Pinia：“把 isLoading 设为 true”。这样用户还没看清接口请求，骨架屏就已经提前在下一页准备好了。


# 后置守卫（afterEach）：
当新页面组件加载完毕、路由跳转彻底完成时，路由守卫再通知 Pinia：“把 isLoading 设为 false”（或者交给页面内随之发起的 Axios 请求来决定何时关闭）。

网络请求和路由跳转自动开关。


# 大致方式

把你代码里的 axios.get(...) 换成你封装好、带有拦截器的那个专属请求工具（比如叫 request.get(...)）。


request .封装axios request控制globle.js全局显示loading显示状态管理
本质拦截器？


v-if 单个元素挂载

v-show 会，标签在 DOM 里，浏览器照常下载图片
# 可能出现的问题
无法提前取消： 如果这 3 秒内用户突然不想看了，想点去别的页面，由于前一个路由被 setTimeout 锁死了，整个路由系统可能会陷入一种非常诡异的交替卡顿状态。

不想看也要看

封装过axios


