---
title: live2dcubismcore.min.js做acync控制组件的异步加载
date: 2026-05-14 10:36:12
tags:
excerpt: live2dcubismcore.min.js做acync控制组件的异步加载
categories: 
    - Vue框架
---
# 基础动态加载函数(看不懂)

首先，我们需要一个可靠的动态脚本加载器。

```
/**
 * 异步加载外部脚本
 * @param {string} src 脚本路径
 */
function loadScript(src) {
    return new Promise((resolve, reject) => {
        const script = document.createElement('script');
        script.src = src;
        script.onload = () => resolve();
        script.onerror = () => reject(new Error(`Script load error: ${src}`));
        document.head.append(script);
    });
}

```

# 结合 Async/Await 的初始化流程(不会)

在组件（如 Vue、React 或原生 JS）中，你可以按照以下逻辑顺序进行控制：

加载核心库 (live2dcubismcore.min.js)。

加载 Framework/Renderer（如果你的项目是将 Framework 抽离的）。

实例化 Live2D 模型。

```
async function initLive2DApp() {
    try {
        console.log("正在加载核心库...");
        // 1. 异步并行或串行加载必要的脚本
        await loadScript('./lib/live2dcubismcore.min.js');
        
        // 2. 确认 Live2D Cubism Core 是否挂载到 window
        if (window.Live2DCubismCore) {
            console.log("Core 加载成功，开始初始化模型...");
            
            // 3. 此时可以安全地调用相关 API
            const model = await setupModel(); 
            startRenderLoop(model);
        }
    } catch (error) {
        console.error("初始化失败:", error);
    }
}


```
# 组件化控制（以现代框架思路为例）（异步组件）

如果你在做一个 Web 组件，可以利用异步加载来减少主包体积（Code Splitting）：

状态管理：设置一个 isLoaded 状态。

占位占位符：在 Core 加载完成前，显示 Loading 动画或静态预览图。

```
class Live2DComponent {
    constructor() {
        this.coreLoaded = false;
    }

    async mount(container) {
        // 展示 Loading 状态
        showSpinner(container);

        // 异步获取核心库
        await loadScript('path/to/live2dcubismcore.min.js');
        this.coreLoaded = true;

        // 初始化渲染器
        this.renderer = new CubismRenderer();
        hideSpinner(container);
    }
}
```
由于 Live2D 的 Core 库通常是作为全局变量挂载到 window 上的

异步组件加载的“嵌套”逻辑

```

用户进入页面：isReady 变为 true。

异步组件启动：Vue 触发 loader。

串行等待（核心点）：

loader 先去请求 /live2dcubismcore.min.js。

浏览器下载并执行 JS，此时 window.Live2DCubismCore 出现。

loader 接着执行 import('./murasame.vue')。

显示组件：所有资源到齐，BoxAnime 消失，模型出现。

```

在 defineAsyncComponent 外部定义一个 Promise 锁


# 来组件通信吧Vue 组件通信（Emit）

状态“反向注入”

这个live2dcubismcore.min.js

加载不好控制
我们控制组件的console.("xxx加载完成")

从挂载开始传值ref（false）

emit("123")


emit('define')