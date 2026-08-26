---
title: ViewModel全局状态管理
date: 2026-06-08 19:09:05
tags: ViewModel全局状态管理
excerpt: ViewModel全局状态管理
categories:
    - Android
---
# 用一句话解释：ViewModel 到底是个啥？
ViewModel 就是一个独立于 UI 界面、专门用来存放“响应式数据（State）”和“业务逻辑（Actions）”的超级大管家。

在前端：它就是 Pinia 的 Store，或者 Vue 3 里的 Composables（比如 useUserStore）。
在 Android：它就是一个普通的类，只要继承了 ViewModel()，它就拥有了掌控全场状态的能力。

# 为什么要搞出一个 ViewModel？直接写在组件里不行吗？
在古老的 Android 开发或者不规范的写法里，大家喜欢把所有数据都用 remember 锁在组件里，或者写在 Activity 页面里。这会带来两个前端极其熟悉的惊天大坑：


## 坑一：页面一旋转/一变大，数据当场人间蒸发（生命周期天坑）
前端场景：用户在写一张长表单，不小心刷新了网页（或者切换了路由再回来），表单里的数据全没了。

Android 场景：在手机上更刺激。用户把手机横过来放（屏幕旋转），或者分屏、切换深色模式。
这时候 Android 系统为了适配新屏幕，会把整个当前页面默默地“销毁，然后重新创建”！如果你用 remember 存数据，页面一转，数据全死，用户直接气炸。

ViewModel 的拯救：ViewModel 的生命周期非常硬。系统销毁页面再重建时，ViewModel 在内存里动都不动。当新页面起来后，会自动重新连上这个 ViewModel，数据零丢失！

## 坑二：跨组件传值让人想砸键盘（无限套娃）
前端场景：组件 A 的数据想给组件 Z，你要一棒一棒往下传（Props Hell），或者用 EventBus。

ViewModel 的拯救：它是全局/局部共享的。组件 A 往 ViewModel 里塞了一个"do”，

组件 Z 只要连上同一个 ViewModel，就能立刻感知到并刷出“dom”，完美实现跨组件共享。

# ViewModel 里的两大核心法宝

一个标准的 ViewModel 内部，通常雷打不动地装着两样东西，这和 Pinia 的设计思路百分之百重合：

| 组成部分 | Android 黑话                  | 前端 Pinia 黑话 | 它是干嘛的 |
|----------|------------------------------|----------------|------------|
| 数据     | mutableStateOf / StateFlow    | state / ref()  | 响应式数据。它变了，UI 自动重组。 |
| 方法     | 函数（fun）                   | actions / 函数 | 业务逻辑。点击、网络请求、数据修改。 |