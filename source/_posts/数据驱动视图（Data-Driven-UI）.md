---
title: 数据驱动视图（Data-Driven UI）
date: 2026-05-16 17:35:22
tags:
excerpt: 数据驱动视图（Data-Driven UI）
categories:
    - Vue框架
---
它之所以看起来高级，是因为它把硬编码的 HTML 结构变成了可以用 JavaScript 控制的“数学公式”。你给它什么数据，它就套用公式自动算出来什么样的界面。

这里面融合了 Vue 3 的两个核心高阶技巧，我们来拆解一下它巧妙在哪里：

# 类名动态映射`（:class="['face', faceClasses[index]]"）`
以前我们要区分 6 个面，必须苦哈哈地手写 6 遍类名（front, back, left...）。

现在我们利用了循环里的 index（0 到 5 递增的数字）作为纽带，在 JS 里定义了一个数组：

`const faceClasses = ['front', 'back', 'left', 'right', 'top', 'bottom'];`

当 Vue 开始数数时：

```
循环到第 1 次（index 是 0）：faceClasses[0] 拿到了 'front' ➡️ 组合成 class="face front"

循环到第 3 次（index 是 2）：faceClasses[2] 拿到了 'left' ➡️ 组合成 class="face left"

高级之处： 用一个数组（数据）去决定 HTML 的类名（样式），把原本死板的标签变成了“填空题”。
```


# 这种“用数据操控一切”的思想，就是 Vue、React 等现代前端框架最核心、最迷人的地方！

