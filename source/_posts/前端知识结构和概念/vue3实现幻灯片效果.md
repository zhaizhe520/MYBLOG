---
title: vue3实现幻灯片效果
date: 2026-05-14 17:13:32
tags: 幻灯片效果
excerpt: 幻灯片效果
categories:
    - Vue框架
---
# 第一步： 安装依赖

首先，在你的项目中安装 Swiper：

`npm install swiper`

大盒子（Swiper 容器）：这是轮播的主体，它决定了轮播图的宽度、高度以及整体的滑动行为。

小盒子（Swiper Slide）：这是你每一页显示的内容。你可以在里面放图片、文字、视频，甚至是一整个复杂的 Vue 组件。

# 第二步：挑选“功能组件”
默认的 Swiper 只是一个可以左右拨动的空壳。如果你想要更多的交互，需要像插 U 盘一样把功能模块插进去：

分页器 (Pagination)：底部的那些小圆点或数字进度条。

导航按钮 (Navigation)：左右两侧的“上一张”和“下一张”箭头。

自动播放 (Autoplay)：让它自己动起来，并设置停顿时间。

滚动条 (Scrollbar)：在底部显示滑动的进度。


# 第三步：配置“行为规则”
这一步是告诉 Swiper 它应该怎么表现。你不需要写逻辑，只需要在配置表里填空：

视图数量：屏幕上一屏显示几个滑块？（比如手机显示 1 个，电脑显示 3 个）。

间距：滑块和滑块之间要留多少像素的缝隙？

循环模式：滑到最后一张时，是停住，还是无缝回到第一张？

响应式设计：你可以设定在屏幕变窄时（比如手机端），自动从一排 3 个变成一排 1 个。

# 第四步：引入样式
这是很多新手容易忽略的一步。Swiper 的布局（怎么排列）和外观（箭头的样子）都是靠 CSS 样式文件 支撑的。

你需要引入它的核心样式来保证能滑动。

如果你用了分页器或导航按钮，还需要额外引入它们对应的皮肤样式，否则按钮要么看不见，要么长得很丑。


基本引入

```
/ 1. 引入组件
  import { Swiper, SwiperSlide } from 'swiper/vue';
  // 2. 引入基础样式
  import 'swiper/css';
  // 3. 引入需要的模块样式（如分页、导航）
  import 'swiper/css/pagination';
  import 'swiper/css/navigation';
  // 4. 引入核心模块
  import { Pagination, Navigation, Autoplay } from 'swiper/modules';

  const modules = [Pagination, Navigation, Autoplay];

```
`https://swiperjs.com/demos?hl=zh-CN`


