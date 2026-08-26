---
title: 基于 Vue 3 + WebGAL 的跨媒介交互式 Live2D 系统
date: 2026-05-09 21:58:21
tags:
excerpt: 基于 Vue 3 + WebGAL 的跨媒介交互式 Live2D 系统
categories: 
  - 項目
---
是的，WebGAL 确实可以将 Galgame 集成到 Vue 3 项目中。由于 WebGAL 本身就是基于 Web 技术（HTML/JS）开发的引擎，它与 Vue 3 的兼容性非常好，非常适合用来制作你提到的那种 Live2D 网页交互项目。

通常有两种主流的集成方式，你可以根据项目的复杂度来选择：


#  使用 Iframe 快速嵌入（最简单）
如果你已经使用 WebGAL 的编辑器打包好了游戏，最快的方法是将生成的静态资源放在 Vue 项目的 public 文件夹下，然后在组件中通过 `<iframe> `引用。

优点：环境隔离，Vue 的样式和逻辑不会干扰到游戏引擎。

缺点：Vue 与游戏之间的数据通信（比如点击游戏角色触发 Vue 组件的变化）需要通过 postMessage 处理，稍微有点麻烦。


# postMessage是什么？



# 作为 NPM 包/组件集成（更深度）
WebGAL 提供了核心渲染库（WebGAL Framework），你可以尝试将其作为依赖项引入。

基本步骤如下：

安装依赖：在 Vue 3 项目根目录执行：
````
Bash
npm install @webgal/framework
```

创建游戏容器组件：
在 Vue 组件中挂载渲染区域。
```
<template>
      <div id="webgal-container" ref="gameCanvas"></div>
    </template>

    <script setup>
    import { onMounted, ref } from 'vue';
    // 引入 WebGAL 相关逻辑
    
    const gameCanvas = ref(null);

    onMounted(() => {
      // 在这里初始化 WebGAL 引擎，绑定到 gameCanvas 元素
    });
    </script>

```
因为 WebGAL 涉及大量的静态资源（图片、音频、Live2D 模型），你需要在 `vite.config.ts` 中处理好资源的路径映射，确保打包后能正常加载。

针对你的项目建议
既然你正在开发基于 **PixiJS** 的 Live2D 项目并使用了 **Pinia**，WebGAL 的优势在于：

*   **资源管理**：它有一套成熟的脚本解析系统，可以帮你省去手动写 PixiJS 渲染逻辑的麻烦。
*   **Live2D 支持**：WebGAL 对 Live2D（如你使用的村雨模型）有很好的原生支持，可以通过简单的文本脚本控制动作和表情。
*   **结合 Pinia**：你可以通过 Vue 的生命周期钩子，将 WebGAL 的播放状态（如剧情进度）同步到 Pinia 中，从而实现“对话结束后解锁 Vue 页面上的某个功能”。

### 注意事项
*   **性能消耗**：Live2D 模型加上 Vue 3 的响应式系统，如果同时运行多个实例，注意移动端的性能表现。
*   **静态路径**：Vue 3 项目打包后路径容易出错，建议始终将游戏素材放在 `public/game/` 这种绝对路径下。
````

# 基于 Vue 3 + WebGAL 的跨媒介交互式 Live2D 系统

| 模块 | 技术栈 | 核心职责 |
| --- | --- | --- |
| 主控容器 (Shell) | Vue 3 (Vite) | 负责整体 UI 布局、侧边栏、导航以及生命周期管理。 |
| 交互引擎 (Engine) | WebGAL (Iframe) | 运行剧情脚本、处理对话选项、播放 Galgame 素材。 |
| 宠物模块 (Pet) | PixiJS + Live2D | 渲染村雨模型，接收来自 Vue 或游戏的信号并做出动作反馈。 |
| 数据中枢 (State) | Pinia | 存储好感度、宠物心情、当前提示词（Tip）等全局状态。 |

# 第一阶段任务清单（本周目标）
既然要参加实习，我们需要先攻克最核心的通信链路：

[ ] 环境搭建：在 Vue 3 项目的 public 文件夹下成功运行 WebGAL 导出的 Demo。

[ ] Iframe 引入：在 Vue 页面中通过` <iframe> `标签显示游戏画面。

[ ] 通信桥梁测试：

在 WebGAL 脚本里写一个 postMessage 脚本。

在 Vue 组件里用 addEventListener 接收并 console.log 打印出来。

[ ] 状态联动：当监听到游戏信号时，成功修改 Pinia 中的一个变量。

# 
# 异步解耦：
资源预加载系统：针对 Galgame 大量的图片和 Live2D 模型，做一个统一的 Loading 进度条，解决“白屏”等待问题。

异步解耦：确保即使 WebGAL 还没加载完，Vue 的主界面和live 2d基本功能（比如点击触摸）也能正常使用。

# 如何联动
这是一个非常硬核且具有实操性的问题！答案是肯定的。如果你使用 iframe 嵌入 WebGAL，你可以通过 窗口间通信（Window Messaging） 让游戏里的“选项”实时控制 Vue 3 里的 Live2D 提示。

这正是展现你工程能力的最佳场景：让两个独立的系统（Vue 和 WebGAL）产生联动。

## 实现逻辑图
WebGAL 端（发送者）：当用户点击“选项”时，发送一个特殊的“信号”出去。

Vue 3 端（接收者）：通过监听器捕捉这个“信号”，然后去修改你 Pinia 里的状态或直接改变 Live2D 的提示词。

### WebGAL 端的设置
在 WebGAL 的剧情脚本里，你需要执行一段自定义的 JavaScript 代码。

指令：在“选项”的分支里，添加一个“执行脚本”指令。

代码内容：
```
// 通知父窗口（Vue）
window.parent.postMessage({
  type: 'GAL_OPTION_SELECTED',
  data: { tip: '你好', action: 'wave' } // 你想传给 Vue 的任何数据
}, '*');

```
### Vue 3 端的设置
在你的 Vue 组件（例如挂载 Live2D 的那个页面）中，添加监听逻辑：
```
import { onMounted, onUnmounted } from 'vue';

// 假设你有一个控制 Live2D 提示的 ref 或 Pinia 状态
const live2dTip = ref('等待中...');

const handleMessage = (event) => {
  // 过滤信号，确保只处理来自 WebGAL 的信息
  if (event.data.type === 'GAL_OPTION_SELECTED') {
    console.log('收到了来自游戏的选项信号:', event.data.data);
    
    // 核心逻辑：控制 Vue 里的 Live2D 提示内容
    live2dTip.value = event.data.data.tip; // 这里就会变成 "你好"
  }
};

onMounted(() => {
  window.addEventListener('message', handleMessage);
});

onUnmounted(() => {
  window.removeEventListener('message', handleMessage);
});


```
# 总结
跨文档通信：你掌握了 postMessage 这种处理不同窗口/框架间数据交换的高级技巧。

解耦设计：游戏逻辑归游戏，UI 逻辑归 Vue，但它们又能通过“信号驱动”达成一致。

状态管理：如果你把收到的“你好”存入 Pinia，那整个 Vue 应用（包括其他的侧边栏或导航栏）都能知道用户在游戏里选了什么。
# 找不到资源 先扒文件吧

## GarBro (推荐)


特点：开源、强大，支持绝大多数 Galgame 引擎。

操作：直接把 .xp3 文件拖进去，它就能像解压软件一样显示里面的内容。你可以直接提取出图片（通常是 .tlg 或 .png）。
强大
`https://github.com/morkt/GARbro`

这些 .xp3 文件本质上是加密或压缩过的资源包，里面藏着你想要的 Live2D 模型、立绘（fgimage）、背景图（evimage）、脚本（scn）以及音频。

# KiriKiri Z+
```
https://github.com/xmoezzz/KrkrExtract
```