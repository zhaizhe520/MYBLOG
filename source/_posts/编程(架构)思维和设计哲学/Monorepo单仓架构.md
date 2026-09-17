---
title: Monorepo 架构
date: 2026-05-11 15:22:08
tags:
excerpt: Monorepo 架构
categories: 
    - Vue框架
---

# Monorepo 架构（单仓库管理）

**Monorepo 就是在一个 Git 仓库里，管理多个独立的项目（主项目、次项目、组件库、工具包*

```
my-enterprise-project/  (根目录)
├── packages/
│   ├── component-lib/  <-- 你的 Vite 库项目（专门产出 dist）
│   │   ├── src/
│   │   ├── vite.config.js
│   │   └── package.json
│   └── utils/          <-- 你的通用 JS 工具函数
├── apps/
│   ├── main-web-app/   <-- 你的 Vue 3 主项目
│   └── admin-panel/    <-- 另一个后台管理项目
├── package.json        <-- 根目录配置
└── pnpm-workspace.yaml <-- 魔法所在：把所有项目串联起来
```

目前企业里实现 Monorepo 最主流的工具是 pnpm。

它通过一个叫 pnpm-workspace.yaml 的文件，告诉电脑：“这些文件夹虽然是独立的，但它们是一家人。”

在主项目里引用库项目时，你只需要运行：


`pnpm add my-lib --workspace`

