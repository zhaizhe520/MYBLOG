---
title: 同类型vue项目合并
date: 2026-05-10 21:15:09
tags:
excerpt: vue项目合
categories:
    - Vue框架
---
当然可以！这种做法在实际开发中非常常见，通常被称为“静态资源引入”或“子应用集成”。

既然你希望把 Vite 组件打包成 dist 放在主项目中使用，主要有以下两种实现方案，你可以根据你的需求选择：

# 方案 1：将组件打包为“库模式”（Library Mode）
修改子组件项目的 vite.config.ts：
```

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

export default defineConfig({
  plugins: [vue()],
  build: {
    lib: {
      entry: path.resolve(__dirname, 'src/components/MurasamePet.vue'), // 组件入口
      name: 'MurasamePet',
      fileName: (format) => `murasame-pet.${format}.js`
    },
    rollupOptions: {
      // 确保外部化处理那些你不想打包进库的依赖
      // 比如主项目已经有 vue 了，就不要把 vue 打包进去
      external: ['vue'],
      output: {
        globals: {
          vue: 'Vue'
        }
      }
    }
  }
})

```
执行打包：运行 npm run build，你会得到一个 dist 文件夹，里面有 XXXX.es.js

把 dist 文件夹拷贝到主项目的 src/assets/vendor/（或者任何你喜欢的目录）。

```
import MurasamePet from '@/assets/vendor/dist/murasame-pet.es.js';
import '@/assets/vendor/dist/style.css'; // 别忘了引入样式
```

# 方案 2：利用 Iframe 嵌入（最快、最隔离）（内联框架）
在 Vue 页面里写“窗户”：

如果你的组件是一个完整的“小工具”，且你不想处理任何代码合并、样式冲突或依赖冲突，那么用 iframe 是最稳妥的。

子组件正常打包：直接执行 npm run build 得到普通的静态 dist。

存放到主项目：将整个 dist 文件夹放入主项目的 public 目录下，重命名为 xxx-app。

主项目调用
```
<template>
  <iframe 
    src="/pet-app/index.html" 
    style="width: 300px; height: 500px; border: none;"
  ></iframe>
</template>
```

# postMessage 是浏览器提供的一种 “跨窗口通信机制”。

它的逻辑非常像“寄信”：投递 -> 监听 -> 拆信。

通过 postMessage 实现了主应用与隔离沙箱内 WEBGAL 模块的指令调度”


传统模式 (Multi-repo)：主项目一个文件夹，组件库一个文件夹。你想在主项目用组件库，得先在库里打包、发布，再到主项目里更新安装。非常折腾。

