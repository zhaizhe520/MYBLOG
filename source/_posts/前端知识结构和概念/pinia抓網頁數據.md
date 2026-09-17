---
title: pinia抓網頁數據
date: 2026-04-28 20:14:26
tags:
excerpt: pinia抓WP數據
categories: 
  - Vue框架
---


# 抓取地址
`域名/wp-json/wp/v2/posts`
這整串是什麼？
這是 WordPress 自帶的官方 REST API 接口地址專門用來抓 WordPress 網站的文章列表
逐段拆解
`域名你 WordPress 伺服器的公網 IP 位址`
`/wp-json/WordPress 固定前綴，只要是 WP 接口，一定帶這個`
`/wp/v2/WordPress REST API 的 版本 2`
`/posts固定路由：拿文章列表`

# 抓取
await axios.get(這個網址)

```
用 axios 向你的 WordPress 發請求，把網站所有文章抓回來抓到的 res 裡面，就是每篇文章標題、內容、發布時間、作者、分類全部資料。
```

# 結構
文章索引規則（重要）
```
res.data[0] → 第一篇文章
res.data[1] → 第二篇文章
res.data[2] → 第三篇文章
以此類推
```
WordPress 給你的文章結構（你用 axios 抓下來會長這樣）
你用 GET /wp-json/wp/v2/posts 抓下來的第一篇文章，結構大概是這樣（我只講關鍵欄位）:

```
id：文章 ID
title.rendered：文章標題
content.rendered：文章全文（HTML 格式）
excerpt.rendered：摘要
date：發布時間

```
# 開始抓
1. 標準請求地址（獲取文章列表）

```
// 僅獲取文章標題、內容
const res = await axios.get("http://xxx//wp-json/wp/v2/posts")
```
2. 獲取特色圖片專用地址（必須加 ?_embed）
```
// 獲取文章 + 特色圖片數據（不加拿不到圖片）
const res = await axios.get("http://xxx//wp-json/wp/v2/posts?_embed")
```
## 細分
1. 文章標題
```
// 第一篇文章標題
res.data[0].title.rendered
```
2. 文章內容（完整 HTML，支持 v-html）

```
res.data[0].content.rendered
```

3. 文章段落提取（純文字）
```
// 獲取所有段落
const paragraphs = res.data[0].content.rendered.match(/<p>.*?<\/p>/g) || []
// 清理 HTML 標籤
paragraphs.map(p => p.replace(/<[^>]+>/g, '').trim())
//第一段
let firstParagraphWithTag = paragraphs[0] || ''

```

4. 特色圖片（最穩、不報錯寫法）
```
// 安全獲取特色圖片鏈接
let imgUrl = ""
try {
  imgUrl = res.data[0]._embedded?.['wp:featuredmedia']?.[0]?.source_url || ""
} catch (e) {}
```

5. 文章標籤（Tags）
```
// 獲取標籤 ID 數組
res.data[0].tags
```
# 分類抓取
```
#類名為9
http://110.42.248.8/wp-json/wp/v2/posts?categories=9&_embed
```
