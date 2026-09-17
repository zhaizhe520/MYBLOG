---
title: vue3打包时自动压缩图片
date: 2026-05-10 16:56:59
tags:
excerpt: vue3打包时自动压缩图片
categories:
    - Vue框架
---
使用 sharp 库： 这是一个非常快的图片处理库。

```
const sharp = require('sharp');
sharp('input.png')
  .webp({ quality: 80 }) // 设置画质为 80
  .toFile('output.webp');

```

使用 Vite 插件： 在你的 Vue 项目里安装 vite-plugin-imagemin，它可以在你打包项目时，自动把图片压缩并转换成 WebP 格式。
# 使用教程

npm install vite-plugin-imagemin -D

配置： 在 vite.config.ts (或 .js) 中加入：

这是 Vite 的中央配置文件。
```
import viteImagemin from 'vite-plugin-imagemin'

export default defineConfig({
  plugins: [
    viteImagemin({
      webp: { quality: 80 } // 自动把符合条件的图转成 webp 或压缩
    }),
  ],
})

```
# 基本格式
```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
// 1. 在顶部导入插件
import viteImagemin from 'vite-plugin-imagemin'

export default defineConfig({
  plugins: [
    vue(), 
    // 2. 在这里加入插件配置
    viteImagemin({
      webp: {
        quality: 80, // 图片压缩后的质量
      },
    }),
  ],
  // ... 其他配置
})

```