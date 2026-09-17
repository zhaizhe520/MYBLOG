---
title: vue3里面不用路由实现按钮对应组件切换
date: 2026-05-09 16:32:11
tags:
excerpt: 不用路由实现按钮对应组件切换
categories: 
    - Vue框架
---
在 Vue 3 中，如果你想实现点击侧边栏切换内容，但不希望发生页面跳转或 URL 路由变化，最优雅的方案是使用 动态组件 (Dynamic Components)。

你可以将右侧区域看作一个“插槽”，通过改变一个变量的值来决定显示哪一个组件。

1. 准备组件
首先，确保你已经创建了 4 个对应的子组件（例如：Home.vue, Profile.vue, Settings.vue, Messages.vue）。

2. 使用 component 标签
Vue 提供了一个内置的 <component :is="..."> 标签，它专门用于这种“不跳转、只切换”的场景。

# 案例
```
<script setup>
import { shallowRef } from 'vue'
// 导入你的 4 个组件
import Home from './components/Home.vue'
import Profile from './components/Profile.vue'
import Settings from './components/Settings.vue'
import Messages from './components/Messages.vue'

// 使用 shallowRef 性能更好，因为组件定义不需要深层响应式
const currentView = shallowRef(Home)

// 定义菜单项
const menuItems = [
  { name: '首页', comp: Home },
  { name: '个人中心', comp: Profile },
  { name: '设置', comp: Settings },
  { name: '消息', comp: Messages }
]

const changeTab = (comp) => {
  currentView.value = comp
}
</script>

<template>
  <div class="container">
    <!-- 左侧/右侧导航栏 -->
    <nav class="sidebar">
      <button 
        v-for="item in menuItems" 
        :key="item.name"
        @click="changeTab(item.comp)"
        :class="{ active: currentView === item.comp }"
      >
        {{ item.name }}
      </button>
    </nav>

    <!-- 右侧内容区 -->
    <main class="content">
      <!-- 这里的 :is 是核心，它会根据 currentView 的值渲染对应组件 -->
      <component :is="currentView" />
    </main>
  </div>
</template>

<style scoped>
.container { display: flex; height: 100vh; }
.sidebar { width: 200px; border-right: 1px solid #ccc; display: flex; flex-direction: column; }
.content { flex: 1; padding: 20px; }
button.active { background-color: #42b883; color: white; }
</style>


```
# 切换动画 (Transition):
如果想要切换时有淡入淡出的效果，可以配合` <Transition> `使用：





# 为什么要用`import { shallowRef } from 'vue'`
在 Vue 3 中，shallowRef 是 ref 的一个“轻量化”版本。

简单来说，ref 会对数据进行深层响应式处理，而 shallowRef 只负责浅层（也就是第一层）的响应式。