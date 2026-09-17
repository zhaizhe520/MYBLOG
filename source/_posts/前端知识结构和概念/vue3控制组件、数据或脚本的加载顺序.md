---
title: vue3控制组件、数据或脚本的加载顺序
date: 2026-05-12 21:35:07
tags:  vue3控制组件、数据或脚本的加载顺序
excerpt: vue3控制组件、数据或脚本的加载顺序
categories:
    - Vue框架
---

1.异步组件 (DefineAsyncComponent)
这是控制组件加载顺序最直接的方法。你可以配合 v-if 或 Promise 来实现“只有当 A 加载完，才开始加载 B”。

场景： 想要推迟某些重型组件的加载（代码拆分）。

做法：
父组件里面写去加载：
异步导入：

```

<scripts setup>
import { ref, onMounted, defineAsyncComponent } from 'vue'
</script>

<template>
<Child v-if="isReady" />
</template>

import { defineAsyncComponent } from 'vue'

const isReady = ref(false);
const AsyncChild = defineAsyncComponent(() =>
  import('./components/HeavyComponent.vue')
)

onMounted(() => {
  isReady.value = true // 挂载后开启显示
})


测试的时候记得写Promise

```

<div style="color:red">
加载慢给她做加载动画
动画时间比加载时间长就好看了
</div>


为什么要写在父组件里？
控制权在父辈： 父组件负责逻辑判断（比如：用户滚动到了底部、用户点击了按钮、或者主数据已经加载完了）。只有父组件点头，浏览器才会去加载子组件的代码。

代码拆分 (Code Splitting)： 当 Vite 或 Webpack 看到 import()（带括号的这种），它会自动把

Live2D.vue 及其引用的库（比如 PixiJS）打包成一个独立的 .js 文件。

这个文件在页面初次打开时不会被下载。

----------------------------------------------
2.使用 v-if 手动控制顺序
这是最通俗易懂的逻辑：通过一个变量作为“开关”，在第一个任务（或组件）完成后，再手动开启第二个任务。

逻辑流：

初始化 showSecond = false。

在第一个组件的 onMounted 或数据请求的 then 回调中，设置 showSecond = true。
-------------------------------------------------------------
3.Suspense (Vue 3 特性)
如果你有多个异步组件（具有 async setup()），Suspense 允许你编排它们的加载状态。

场景： 等待一组异步任务全部完成，或按层级展示。

用法：
```
<Suspense>
      <template #default>
        <AsyncComponent/> <!-- 这里的组件内部可以写 await -->
      </template>
      <template #fallback>
        加载中...
      </template>
    </Suspense>
```
-----------------------------
4.逻辑依赖型（必须 A 好，B 才能动）

这通常发生在 setup 语法糖中。利用 await 的阻塞特性，你可以精准控制代码执行流：

```
// 在 <script setup> 中
const dataA = ref(null)
const dataB = ref(null)

// 顺序执行：先拿 A 的数据，再拿 B 的数据
const init = async () => {
  dataA.value = await fetchA() // 阻塞直到 A 完成
  dataB.value = await fetchB() // A 完成后才开始加载 B
}
init()
```

5.组件层级控制
如果你有多个子组件，想让它们一个接一个地出现，可以结合 onMounted 钩子
父组件挂载后再触发子组件的显示：
```
<template>
  <ChildA v-if="step >= 1" @ready="step = 2" />
  <ChildB v-if="step >= 2" @ready="step = 3" />
</template>

<script setup>
import { ref } from 'vue'
const step = ref(1)
</script>
```

# 总结一下：

要快： 用 defineAsyncComponent（让不重要的组件靠后加载）。
要稳： 用 async/await（确保数据获取有先后）。
要视觉效果： 用 v-if 配合 Transition 动画。

# 我们可以把这个过程拆解成 “代码异步” 和 “资源异步” 两步走：

