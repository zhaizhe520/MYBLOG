---
title: Vue3處理跨域問題
date: 2026-05-05 00:14:41
tags: 跨域問題
excerpt: 跨域問題
categories:
    - Vue框架
---
# 跨域问题 (CORS) 是怎么产生的？
这其实是浏览器的安全保护机制（同源策略）。

规则：如果你的前端地址（Vue）和后端地址（WordPress）的 协议、域名、端口 只要有一个不一样，浏览器就会拦截它们之间的通信。

典型场景：

Vue 开发服务器跑在：http://localhost:5173

WordPress 跑在：http://localhost:8080 (或者是 [www.your-wp.com](https://www.your-wp.com))

当 Axios 去请求数据时，浏览器发现“门牌号”不一样，直接报错：CORS error。

# 怎麼解決 方案 A：开发环境用 Vite Proxy（最方便）
这就是让 Vite 充当“中间人”。Vue 向 Vite 发请求，Vite 悄悄去问 WordPress 要数据，最后再给 Vue。因为服务器之间通讯不涉及跨域。

Vite Proxy 是怎么工作的？（原理）
```

Vite Proxy 在你的电脑里搭建了一个“中转站”。

伪装：Vue 不再直接请求远程的 wordpress.com，而是请求自己（Vite 服务器），例如请求 /api/posts。

同源：对浏览器来说，localhost:5173 请求 /api/posts 是同源的，所以浏览器放行。

转发：Vite 服务器收到请求后，在后台（Node.js 环境）悄悄把这个请求转发给真正的 WordPress 接口。

绕过限制：跨域限制只存在于浏览器。 服务器与服务器之间的通讯（Vite 到 WordPress）是没有跨域问题的。

返回：Vite 拿到数据后，再转手交给 Vue。

```
# 線上的話 后端 CORS (Cross-Origin Resource Sharing)跨域资源共享
1. 定义：它是一张“通行证”
CORS 全称是 Cross-Origin Resource Sharing（跨域资源共享）。它是一种基于 HTTP 响应头（Response Headers）的机制。

简单来说：跨域是浏览器的限制，而 CORS 是后端给浏览器的“解禁指令”。 当浏览器发现请求跨域时，会检查后端返回的报文里有没有特定的“通行证”，如果有，就允许数据通过。
-------------------------
2. 核心原理：关键的响应头
后端通过在响应中加入以下几个关键字段来告诉浏览器：

Access-Control-Allow-Origin：最重要的字段。指定允许哪些域名访问。

例如：Access-Control-Allow-Origin: [https://vue-app.com](https://vue-app.com)（只允许这个域名）。

或者：Access-Control-Allow-Origin: *（允许所有人，通常不安全）。

Access-Control-Allow-Methods：允许哪些请求方式（GET, POST, PUT 等）。

Access-Control-Allow-Headers：允许前端在请求头里带哪些自定义字段（比如 Authorization 令牌）。
------------------------
# 總結
“CORS 是后端的声明式解决手段。它的原理是在 HTTP 响应头中注入 Access-Control-Allow-Origin 等字段。浏览器在收到请求结果后，会校验这些字段，如果匹配成功，则允许前端 JS 读取数据。对于复杂请求，浏览器还会先发起一个 OPTIONS 预检请求 来确认安全性。相比 Nginx 代理，CORS 更灵活，但需要后端额外编写逻辑或配置。”