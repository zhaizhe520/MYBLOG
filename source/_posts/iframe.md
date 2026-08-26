---
title: iframe HTML 内联框架标签
date: 2026-06-25 10:44:11
tags: iframe HTML 内联框架标签
excerpt: iframe HTML 内联框架标签
categories: 
    - Vue框架
---
<div style="color:pink">HTML 内联框架标签</div>

隔离原理：浏览器自带的硬隔离。借由同源策略，主应用和 iframe 之间的 CSS、DOM、Window 全局变量都是完全隔离的，互不影响。


 `<iframe src="/xxx/xxx.html" class="" frameborder="0"></iframe>`


样式绝对隔离（CSS Isolation）：Vue 主应用里的全局样式、组件样式，绝对不会影响到 /game/index.html 里面的游戏样式；反之，游戏里的样式也不会把主应用搞垮。

脚本绝对隔离（JS Sandbox）：游戏里如果定义了 window.score = 100，主应用的 window.score 是拿不到的，它们拥有各自独立的 window 全局对象。

崩溃隔离：如果游戏由于内存泄漏或代码报错崩溃了，主应用通常仍能正常运行，不会导致整个网页白屏。


# 社区基于 iframe 封装的组件（如 v-iframe）

在 Vue 生态里，直接操作原生 iframe 有时候不太方便（比如组件通信、加载状态监听）。很多人会使用或自己封装一个类似 `<v-iframe>`的组件来方便在 Vue 3 中使用。

# “微前端”隔离方案

如果你是在做大系统拆分，觉得原生 iframe 刷新后状态丢失、路由无法同步，你可能是在找以下微前端框架中的“隔离沙箱”：

wujie（无界）：腾讯开源的微前端框架。它最核心的特点就是基于 `<iframe>` 实现 JavaScript 沙箱隔离，同时把 DOM 渲染到 Shadow DOM 中。它完美解决了原生 iframe 的性能和路由问题，在 Vue 3 中非常常用。

qiankun（乾坤）：阿里开源的微前端框架。它没有用 iframe，而是自己实现了一套 JS 代理沙箱（Proxy Sandbox）和 CSS 样式隔离


# Vue 3 中如何与 iframe 进行“跨界通信”

主要就是`console.log`到浏览器进行消息传递

从游戏（iframe 内部）向 Vue 主应用发送消息

向父窗口发送数据 postMessage

```
// 游戏通关时，向父窗口发送数据
window.parent.postMessage({
  type: 'GAME_OVER',
  score: 999
}, '*'); // '*' 可以换成你主应用的域名以确保安全
```
// 定义接收消息的处理函数

`console.log`打印到浏览器

组件挂载时启动监听

组件销毁时记得移除监听，防止内存泄漏

# 使用 iframe 的三大“大坑”

虽然隔离性极佳，但你在写 Vue 3 的时候一定要注意这三个问题：

路由与状态刷新丢失：如果用户在游戏里玩到了第 3 关，此时用户刷新了整个 Vue 页面，`<iframe>` 会直接重置回 /game/index.html（第 1 关）。它的内部状态不会自动保存到 Vue 的路由或 Pinia 中。

遮罩层/弹窗无法越界：如果游戏内部弹出一个 Modal 弹窗，它只能在 class="game-frame" 这个小框框里居中，绝对无法盖住整个浏览器屏幕。

加载白屏与性能：`<iframe>`相当于重新加载了一个完整的 DOM 树和执行环境，比较消耗内存。建议加一个 v-if 或者 loading 状态，等主页面渲染完后再加载游戏。