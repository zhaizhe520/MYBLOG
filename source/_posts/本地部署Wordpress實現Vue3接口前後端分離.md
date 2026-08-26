---
title: 本地部署Wordpress實現Vue3接口前後端分離
date: 2026-04-28 15:32:27
tags:
excerpt : 前後端分離
categories:
    - WordPress
---
# 一、先给你讲清楚：什么是前后端分离（超简单）
WordPress = 后端 / 数据中心
存文章
存图片
存用户
自动生成 API 接口（不用你写）
Vue3 = 前端 / 展示页面
做界面
做交互
做样式
通过 API 去 WordPress 拿数据
它们的关系：
Vue ← API ← WordPress
# 二、你本地具备的条件（全部满足）
✅ 本地 WordPress 能跑（你已经登录后台了）
✅ Vue3 项目能跑、能打包
✅ 数据库 MySQL 你已经会进了
✅ 你只差：Vue 调用 WP API
1. 找到 WordPress 自带的 API 地址
不用插件、不用配置自带接口例如：
```
plaintext
http://localhost/你的WP地址/wp-json/wp/v2/posts
打开就是文章列表 JSON
```
2. Vue 安装 axios（发送请求）
```
plaintext
npm install axios
```
3. Vue 里写 3 行代码请求 WP 数据
js
axios.get('http://localhost/.../posts').then(res => {
  console.log(res.data)
})catch(error){
  console.log("抓取失敗")
}
4. 把数据渲染到 Vue 页面
```
html
预览
<div v-for="item in list" :key="item.id">
  {{ item.title.rendered }}
</div>
```


# 什麼是axios

簡單來說，Axios 是一個基於 Promise 的 HTTP 客戶端，專門用來在瀏覽器（Frontend）和 Node.js（Backend）環境中發送網路請求。

如果你曾經被原生 JavaScript 那囉唆的 XMLHttpRequest 搞得頭大，或者覺得 fetch 的某些功能（如自動轉換 JSON）還不夠便利，那 Axios 就像是你的「網路請求懶人包」。

🚀 為什麼大家愛用 Axios？
Axios 之所以成為開發者的首選，主要有以下幾個核心特性：

基於 Promise： 它完全支持現代的 async/await 語法，讓非同步代碼看起來像同步一樣整潔。

同構性 (Isomorphic)： 同一套代碼可以運行在瀏覽器端，也可以運行在 Node.js 伺服器端。

自動轉換 JSON： 發送請求時它會幫你把物件轉成 JSON，收到回應時也會自動解析成 JavaScript 物件，省去手動 .json() 的步驟。

攔截器 (Interceptors)： 這是它的殺手鐧。你可以在請求發出前（例如統一加上 Auth Token）或回應回來後（例如統一處理 401 錯誤）進行攔截處理。

請求取消： 內建機制可以讓你取消尚未完成的請求，避免浪費資源。

客戶端防禦 XSRF： 內建了一些基本的安全性保護。

----------------------------------------
## 簡單的git
```
import axios from 'axios';

async function getUserData() {
  try {
    // 不需要手動轉 JSON，response.data 直接就是結果
    const response = await axios.get('https://api.example.com/user/123');
    console.log(response.data);
  } catch (error) {
    // 只要 API 回傳 4xx 或 5xx，都會直接進到這裡
    console.error('抓取失敗：', error.message);
  }
}
```

# 🎯總結
Axios 就是一個更聰明、功能更完整的網路請求工具。對於小型專案，原生 fetch 可能就夠了；但如果你在開發中大型應用，需要統一處理 Token、錯誤提示或複雜的 API 邏輯，Axios 會幫你省下大量的開發時間。
