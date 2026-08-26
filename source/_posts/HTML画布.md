---
title: HTML画布
date: 2026-06-02 10:09:34
tags: svg和canvas
excerpt: svg和canvas
categories:
    - HTML
---


|  | SVG | Canvas |
| ---- | ---- | ---- |
| 怎么画 | 声明式，HTML 标签 <path> <circle> | 命令式，JS ctx.lineTo() |
| 适合 | 图标、Logo、插图 | 游戏、实时图表、粒子特效 |
| 缩放 | 无限清晰 | 放大了会糊 |
| 交互 | 每个元素都能绑 @click | 只能绑整个 画布 |

SVG 是"告诉浏览器画什么"，Canvas 是"告诉浏览器怎么画"。那个外链图标用 SVG 就行——3 个标签，点一下就能知道位置，放 Canvas 里还得写坐标计算。