---
title: vue組件小項
date: 2026-05-04 09:18:43
tags:
excerpt: vue安裝遇到的問題
categories:
  - Vue框架
---
# 安裝項目
1.vite形（輕量）
`
項目文件夾中 npm create vite@latest
`
2.完整
`
項目文件夾中 npm create vue@latest
`
# 安裝 pinia,axios，rounter等
`
npm install pinia
`
掛載
main.js
```
import { createApp } from 'vue'
import { createPinia } from 'pinia' // 1. 引入工廠函式
import App from './App.vue'

const app = createApp(App)
const pinia = createPinia() // 2. 創建 Pinia 實例

app.use(pinia) // 3. 註冊插件 (這步最關鍵！)
app.mount('#app')掛載
```
-------------------------
沒事過我是分開寫在pinia庫里的
`
npm install axios
`
而是建立一個統一的工具檔案：src/utils/request.js
用直接導入
import request from '../utils/request' // 引入剛才設定的 axios

```
import axios from 'axios'

const service = axios.create({
  baseURL: 'https://api.your-pet-server.com', // 之後如果有後端可以寫這
  timeout: 5000
})

// 可以在這裡加攔截器，例如處理錯誤訊息
service.interceptors.response.use(
  response => response.data,
  error => {
    console.error('請求出錯了：', error)
    return Promise.reject(error)
  }
)

export default service
```