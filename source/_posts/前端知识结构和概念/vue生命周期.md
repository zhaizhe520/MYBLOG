---
title: vue生命周期
date: 2026-06-10 21:38:46
tags: vue生命周期
excerpt: vue生命周期
categories: 
    - 面试
---
Vue 生命周期简述

Vue 生命周期指组件实例从创建、初始化、挂载、更新到销毁的完整运行过程，过程中会自动执行一系列「生命周期钩子函数」，让开发者可以在特定阶段插入自定义逻辑。
核心阶段与钩子（按执行顺序）


# 组合式API生命周期钩子对照表

生命周期钩子 (组合式 API)|此时能访问 DOM 吗？|核心适用场景
----|----|----
setup()|❌ 否|核心业务逻辑、初始化响应式数据、发首屏请求
onBeforeMount|❌ 否|极少使用
onMounted|静态/动态 DOM 均可|操作 DOM、初始化第三方插件（ECharts 等）
onBeforeUpdate|❌ 旧 DOM|更新前读取旧 DOM 状态（如滚动条位置）
onUpdated|最新 DOM|依赖最新 DOM 的后续操作（避免在此处改数据）
onBeforeUnmount|依然可用|清除定时器、解绑全局事件、防止内存泄漏（最常用）
onUnmounted|❌ 否|最后的清理工作

# 创建阶段 (Creation)

（实例初始化，无真实 DOM）

在 Vue 3 的 `<script setup>` 中，没有 beforeCreate 和 created 这两个钩子函数了。

因为 setup() 自身就在这两个钩子之前并围绕它们运行。

 <div style="color:pink">setup() / beforeCreate / created</div>

组件在干什么：Vue 正在初始化响应式数据（Ref, Reactive）、计算属性（Computed）和方法（Methods）。此时 DOM 还完全没有创建。

发送初始的异步请求（如 fetch/axios 获取页面首屏数据）。

初始化非响应式的变量。

注意：此时绝对不能操作 DOM，因为页面上还什么都没有。


# 挂载阶段 (Mounting)

<div style="color:pink">onBeforeMount() (挂载前)</div>

组件在干什么：组件的 HTML 结构已经在内存中编译好了，但还没有真正挂载（插入）到浏览器的页面DOM中。

我们能干什么：这个钩子在实际开发中极少使用。如果你有一些逻辑必须在 DOM 生成前执行，且不能放在 setup 里，可以放这里。

<div style="color:pink">onMounted() (挂载完成)</div>

组件在干什么：组件的 DOM 已经全部渲染完成，并成功插入到了浏览器页面中。

我们能干什么：这是开发中最常用的钩子之一。

操作 DOM：获取节点、获取元素的宽高、聚焦输入框等。

初始化第三方库：如实例化 ECharts 图表、Swiper 轮播图、视频播放器等（这些库通常需要绑定真实的 DOM 节点）。

发送需要依赖 DOM 的网络请求。

# 更新阶段 (Updating)

 <div style="color:pink">onBeforeUpdate() (更新前)</div>

当组件使用的响应式数据发生变化，触发视图重新渲染时执行。

组件在干什么：响应式数据已经变了，但是浏览器页面上的 DOM 还没有更新。

我们能干什么：

在 Vue 更新 DOM 之前，访问现有的 DOM 状态（例如，记录滚动条当前的位置）。

可以在这里进一步修改状态，不会触发额外的重新渲染。

 <div style=" color:pink">onUpdated() (更新完成)</div>

组件在干什么：Vue 已经把最新的数据渲染到了页面上，DOM 已经完成了更新。

我们能干什么：

操作更新后的 DOM。例如，根据内容的变化重新计算滚动条位置。

警告：尽量避免在这里修改响应式数据，否则极其容易陷入“数据变->触发更新->修改数据->再触发更新”的死循环。


# 卸载阶段 (Unmounting)

当组件从页面中被移除（例如通过 v-if 隐藏，或者路由跳转切换页面）时触发。

<div style="color:pink">onBeforeUnmount() (卸载前) </div>

组件在干什么：组件马上就要被销毁了，但此时组件的所有功能、DOM、响应式数据都还完全可用。

我们能干什么：这是非常重要的“收尾/扫除”阶段，用来防止内存泄漏。

清除定时器（setInterval, setTimeout）。

解绑全局事件监听器（如 window.addEventListener('resize', ...)）。

断开 WebSocket 连接。

销毁第三方库的实例。

 <div style="color:pink">onUnmounted() (卸载完成)</div>

组件在干什么：组件已经彻底销毁。所有的指令已解绑，所有的事件监听器已被移除，所有的子组件也都被销毁。

我们能干什么：可以用来做一些最后的清理确认，或者向父组件/全局状态发送通知。通常大部分清理工作在 onBeforeUnmount 已经做完了。

# 特殊钩子


| 钩子名称 | 适用场景 | 核心作用 |
| ---- | ---- | ---- |
| onActivated | &lt;KeepAlive&gt; 缓存组件 | 组件显示时：恢复状态/更新数据 |
| onDeactivated | &lt;KeepAlive&gt; 缓存组件 | 组件隐藏时：暂停功能/保存状态 |
| onErrorCaptured | 任何父/祖先组件 | 拦截后代组件错误，防止白屏崩溃 |
| onRenderTracked | 开发调试 | 监控哪些数据被组件依赖了 |
| onRenderTriggered | 开发调试 | 精准定位是谁触发了组件的重新渲染 |
| onServerPrefetch | 服务端渲染 (SSR) | 在服务器端提前异步加载数据 |

##  缓存组件专属钩子 (KeepAlive) 

当组件被 Vue 的 `<KeepAlive>` 包裹时，组件在切换时不会被真正卸载，而是会被缓存（处于休眠状态）。

这时前面说的 onUnmounted 就不会触发了，取而代之的是下面两个钩子：

<div style="color:pink">onActivated() (激活时)</div>

什么时候触发：组件被 `<KeepAlive>` 缓存了，当它重新显示在页面上时触发（伴随首次挂载也会触发一次）。

我们能干什么：

重新获取页面数据，保证缓存的页面内容是最新的。

恢复之前记录的页面滚动位置。

<div style="color:pink">onDeactivated() (失活时)</div>

什么时候触发：组件被切换走，进入缓存/隐藏状态时触发。

我们能干什么：

暂停正在运行的功能（如暂停视频播放、暂停倒计时）。

保存当前页面的状态（如记录当前滚动条位置

## 错误处理钩子 (Error Handling)

<h1>onErrorCaptured() (捕获后代组件错误)</h1>

什么时候触发：当任何后代组件在执行生命周期钩子、事件处理器或响应式数据副作用时抛出错误，这个钩子就会被触发。

我们能干什么：

全局/局部错误上报：把错误日志发送到服务器（如 Sentry）。

错误降级处理：可以修改状态，在页面上显示一个“组件加载失败，请重试”的友好提示，防止整个应用直接白屏崩溃。

特殊机制：如果在这个钩子里返回 false，可以阻止这个错误继续向上冒泡传给全局的 app.config.errorHandler。

# 开发与调试钩子 (Debugging) 重型侦察武器

<h1>onRenderTracked() (收集依赖时)</h1>

什么时候触发：组件在渲染过程中，只要有响应式数据（如 ref）被读取/追踪了，就会触发。

我们能干什么：检查组件到底依赖了哪些数据。

<h1>onRenderTriggered() (触发渲染时)</h1>

什么时候触发：当某个响应式数据发生变化，导致组件准备重新渲染的那一刻触发。

我们能干什么：精准定位是谁修改了数据导致了组件的更新，结合控制台的 event 对象可以看清是哪行代码改了什么值，是做性能优化的利器。

# 服务端渲染专用 (SSR) 全栈前端（Nuxt.js 等服务端渲染框架）的专属

<h1>onServerPrefetch() (服务端预取)</h1>

什么时候触发：仅在服务端渲染（SSR）期间，组件实例在服务器上被渲染之前执行。

我们能干什么：

在服务器端就把异步数据请求好（比如直接查数据库或调用内部 API），然后把带数据的 HTML 直接发送给浏览器，有利于 SEO 和首屏速度。

