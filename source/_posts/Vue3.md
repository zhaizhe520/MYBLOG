---
title: Vue3
date: 2026-09-03 15:07:38
tags:
---
<details>
<summay>响应式API</summay>

```
computed（计算属性）：

用途：基于现有的响应式变量（ref / reactive）进行纯数学计算或格式化转换（如算坐标、算总价、过滤列表）。

要有实际变化的
```

```
onMounted（挂载完成生命周期）：

用途：获取真实的 DOM 元素宽高、初始化 PixiJS 画布、绑定原生 window 监听事件（如 addEventListener('resize')）。
```
```
watch（侦听器）：

用途：当变量改变时，去执行有副作用的操作（如重新发送 API 接口请求、播放音效）。
```

</details>

```
computed（计算属性）：

用途：基于现有的响应式变量（ref / reactive）进行纯数学计算或格式化转换（如算坐标、算总价、过滤列表）。
```

```
onMounted（挂载完成生命周期）：

用途：获取真实的 DOM 元素宽高、初始化 PixiJS 画布、绑定原生 window 监听事件（如 addEventListener('resize')）。
```
```
watch（侦听器）：

用途：当变量改变时，去执行有副作用的操作（如重新发送 API 接口请求、播放音效）。
```

Vue 自定义指令，就是导出一个对象，对象上挂载 `mounted / updated / unmounted` 这些函数，Vue内部会在对应生命周期自动调用这些函数，传入 `el、binding、vnode、prevVNode` 参数。