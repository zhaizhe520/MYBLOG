---
title: Vue3實現搜索框數據搜索
date: 2026-04-26 19:53:38
tags: 搜索框數據搜索
categories:
    - Vue框架
---
準備一份本地寫死的陣列數據，不用請求後端。
綁定輸入框內容，拿到你輸入的關鍵字。
用篩選邏輯：
遍歷全部數據
判斷標題 / 文字有沒有包含輸入的關鍵字
不分大小寫、模糊匹配，包含就顯示，不包含隱藏。
輸入空內容時，自動還原顯示全部列表。
# 實現流程

```

<template>
  <div class="search-container">
    <!-- 搜索输入框 -->
    <input
      v-model="keyword"
      type="text"
      placeholder="搜索内容..."
      class="search-input"
    />

    <!-- 搜索结果列表 -->
    <ul class="list">
      <li v-for="item in filteredList" :key="item.id" class="list-item">
        {{ item.name }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// 本地模拟数据（代替后端）
const dataList = ref([
  { id: 1, name: 'Vue3 前端开发' },
  { id: 2, name: 'React 框架学习' },
  { id: 3, name: 'CSS 渐变背景' },
  { id: 4, name: 'JavaScript 基础' },
  { id: 5, name: '无后端搜索功能' },
])

// 搜索关键词
const keyword = ref('')

// 计算属性：自动过滤结果（核心搜索逻辑）
const filteredList = computed(() => {
  // 没有关键词时返回全部
  if (!keyword.value) return dataList.value

  // 有搜索词 → 模糊匹配过滤
  return dataList.value.filter(item =>
    item.name.toLowerCase().includes(keyword.value.toLowerCase())
  )
})
</script>

```
# 建立 Pinia 倉庫（放共用數據）
## 第一步：先確認專案有安裝 Pinia
Vue3 專案一般預設自帶。沒有的話只裝一次：

```
plaintext
npm install pinia

```
## 第二步：建立 Pinia 倉庫（放共用數據）
```

在 src 裡新建資料夾：stores
裡面新建檔案：listStore.js

```
把原本寫在別的組件 data 裡的全部數據，整個搬來這裡
👉 意思：原本數據藏在單一組件 → 現在拿出來放「公共數據庫」所有組件都能讀、都能搜。

## 第三步：把原始數據搬進 Pinia
原本某個組件私有的列表數據，全部剪下來，貼進 Pinia 的 state 裡。這份數據就變成：全域共用、任何頁面 / 組件都能直接取用。

## 第四步：搜索框組件引入 Pinia 倉庫
在搜索框那個 vue 組件
引入你剛才建立的 listStore
直接拿到完整原始數據
不用跨組件傳值、不用 props、不用事件。
第五步：你的搜索邏輯直接用
輸入框：v-model="keyword"
過濾的來源數據：直接抓 Pinia 裡的公共列表
輸入文字 → 過濾 Pinia 的數據 → 顯示結果
# 總結
把別組件的私有數據 → 搬到 Pinia（公共倉庫）
搜索組件引入 Pinia
拿 Pinia 的數據 + v-model關鍵字 做本地過濾
完成跨組件搜索，永久好用

# 好難我不會，什麼關鍵詞的懶搜索的
搞不名白aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa

# 第二次部署

這是一個非常經典的開發場景！針對你的問題「需要前後端結合還是前端就行了？」，答案通常取決於你的數據量：

數據量小（例如 < 500 條卡片）： 只要前端處理就行。一次性從 WordPress (WP) 抓取所有數據存入 Pinia，然後用 computed 進行過濾。這樣搜尋起來「秒出結果」，體驗最好。

數據量極大（例如幾千幾萬條）： 需要前後端結合。透過 API 發送關鍵字給 WP，由後端過濾後只回傳匹配的結果。

既然是個人作品集或一般展示頁（從你的代碼看來），「前端過濾」 是最推薦的做法。

這是一個非常經典的開發場景！針對你的問題「需要前後端結合還是前端就行了？」，答案通常取決於你的數據量：

數據量小（例如 < 500 條卡片）： 只要前端處理就行。一次性從 WordPress (WP) 抓取所有數據存入 Pinia，然後用 computed 進行過濾。這樣搜尋起來「秒出結果」，體驗最好。

數據量極大（例如幾千幾萬條）： 需要前後端結合。透過 API 發送關鍵字給 WP，由後端過濾後只回傳匹配的結果。

既然是個人作品集或一般展示頁（從你的代碼看來），「前端過濾」 是最推薦的做法。

## 🛠️ 實作步驟
我們要利用 Vue 3 的 Computed (計算屬性)。它的邏輯是：當搜尋框的文字變動時，自動計算出一組「新的陣列」給頁面渲染。

1. 修改 Pinia Store (useWorkStore)
在 Store 裡面增加一個 searchQuery 來存放搜尋關鍵字。
```
export const useWorkStore = defineStore('work', {
  state: () => ({
    searchQuery: '', // 新增：存放搜尋文字
    pageAllData: {
      1: [{ textName: 'Vue 專案', imgSrc: '...' }, { textName: 'React 專案', imgSrc: '...' }],
      2: [], 
      3: [], 
      4: []
    }
  }),
  getters: {
    // 新增：過濾後的數據
    filteredData(state) {
      // 1. 先把物件裡所有的陣列攤平（Flatten）成一個大陣列
      const allItems = Object.values(state.pageAllData).flat();
      
      // 2. 根據關鍵字過濾
      if (!state.searchQuery) return allItems; // 沒搜尋時顯示全部
      
      return allItems.filter(item => 
        item.textName.toLowerCase().includes(state.searchQuery.toLowerCase())
      );
    }
  }
});
```