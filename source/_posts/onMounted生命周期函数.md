---
title: onMounted生命周期函数
date: 2026-05-14 13:01:22
tags:
excerpt: 生命周期
categories :
    - Vue框架
---

onMounted 是什么？
在 Vue 3 (Composition API) 中，onMounted 是一个生命周期钩子函数。

简单来说，它的作用是：当组件的 HTML 结构已经生成并成功“挂载”（渲染）到浏览器页面上时，触发执行的函数。

1.核心执行时机
在一个组件的生命周期中，加载过程大致如下：

Setup/Created：组件实例刚创建，此时代码里只有数据，页面上还没有任何 HTML 元素。

Mounting：Vue 开始把你的代码转成真实的 DOM。

Mounted（触发点）：HTML 已经完全出现在屏幕上了。

2.为什么要用它？（常见场景）
在 setup 顶层（非 onMounted 内部），你无法操作网页元素，因为它们还没生出来。你必须在 onMounted 里做以下事情：

操作 DOM 元素：比如通过 ref 获取 Canvas 标签、设置焦点、或者初始化像 Live2D 这样需要绑定 HTML 节点的插件。

发送网络请求：虽然在 setup 里也能发请求，但很多开发者习惯在挂载后启动初始化数据的拉取。

启动定时器或监听器：比如启动一个计时器，或者监听窗口大小变化（window.addEventListener）。

emit 的本质：事件驱动

<div style="color:pink">通过子传父来实现和挂载onMounted来实现空白组件的控制</div>

为什么 emit 监听比 loader 更好？真正的“完成”：core.js 下载完 live2d 模型显示出来。

模型初始化涉及二进制文件解析、纹理上传 GPU、着色器编译。这些 CPU/GPU 的耗时，loader 根本看不见。颗粒度控制：

你可以自己定义什么时候发送信号。比如：是模型刚出现就取消动画？还是等模型做完一个“打招呼”的开场动作后再取消动画？稳定性：哪怕网络波动导致组件加载极快，但设备性能差导致渲染慢，

emit 也能确保用户看到的始终是加载完的画面，不会出现“黑屏挂载”。



emit('ready',true)

emit('ready',false)



在父组件 App.vue 中接收信号