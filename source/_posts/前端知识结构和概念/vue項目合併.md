---
title: vue項目合併
date: 2026-05-07 15:34:18
tags: vue項目合併
excerpt: 企業級 Vue 項目合併方案：從物理搬運到微前端架構
categories:
    - Vue框架
---
# 方案一：Monorepo（大倉庫架構）—— 高效協作的首選
適用場景：多個項目共用大量組件、工具函數（Utils）或 UI 規範，且希望在同一個 Git 倉庫中管理。

核心技術：PNPM Workspaces

PNPM 是目前 Monorepo 的主流選擇，它通過軟連結（Symlinks）極大地節省了磁碟空間。

/my-monorepo
├── package.json
├── pnpm-workspace.yaml  <-- 定義子項目路徑
├── packages
│   ├── main-app         <-- 主 Vue 項目
│   ├── shared-utils     <-- 通用工具包
│   └── feature-service  <-- 原 Vite 項目

配置 pnpm-workspace.yaml：
```
packages:
  - 'packages/*'
```

賴共享：
在 main-app 中直接引用 feature-service：
pnpm add feature-service --filter main-app

優點：單次 pnpm install 搞定所有依賴；代碼改動實時熱更新；依賴版本易於統一。

# 方案二：微前端（Micro-frontends）—— 巨型項目的終極解法

適用場景：項目規模巨大、技術棧版本不一（如 Vue 2 要接 Vue 3），或由不同團隊獨立維護、獨立部署。

推薦工具：Wujie (無界) 或 Qiankun

微前端的核心思想是將 Web 應用拆解成多個可以「獨立運行」的微應用。

主應用 (Host)：負責導航定位和子應用加載。

子應用 (Remote)：原來的 Vite 項目，只需做少量修改（如配置跨域、生命週期鉤子）。

核心邏輯：
```
// 在主應用中註冊
import { startApp } from "wujie";

startApp({ name: "vite-project", url: "http://localhost:5173/", el: "#container" });
```
優點：應用隔離。CSS 和 JS 運行在沙箱中，完全不用擔心 A 項目的樣式污染了 B 項目；支援跨框架。

# 方案三：庫模式（Library Mode）—— 功能模組化

適用場景：Vite 項目本質上是一個功能模組、插件或 UI 組件庫，供其他項目調用。

實施步驟：

修改 Vite 配置：將項目從應用模式改為庫模式。
```
// vite.config.ts
export default defineConfig({
  build: {
    lib: {
      entry: './src/index.ts',
      name: 'MySharedLib',
      fileName: (format) => `my-lib.${format}.js`
    },
    rollupOptions: {
      external: ['vue'], // 關鍵：不打包 vue，減少體積
      output: { globals: { vue: 'Vue' } }
    }
  }
})

```
發布或連結：

私有 NPM：打包後發布到公司內部的 NPM 私服（如 Verdaccio）。

本地連結：使用 pnpm link 或 npm link 在本地進行測試。

優點：代碼高度解耦，複用性最強，適合沉澱技術資產。

# 總結
在選擇合併方案時，可以參考以下決策樹：

| 场景描述 | 推荐方案 |
| :--- | :--- |
| 如果不希望改變部署流程，且想共享依賴 | 選 Monorepo |
| 如果兩個項目完全獨立，只是想在一個頁面顯示 | 選 微前端 |
| 如果一個項目是為另一個項目服務的工具集 | 選 庫模式 |
| 如果是短期快速上線的小項目 | 直接 物理合併（拷貝代碼），雖然原始但有效 |
在合併過程中，最難的往往不是代碼，而是環境變量（.env）和路由守衛的統一。建議在合併前先列出兩邊的依賴清單，避免 vue-router 或 pinia 版本衝突導致的白屏問題！