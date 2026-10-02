---
title: Node后端
date: 2026-10-01 22:57:12
tags: Node后端
excerpt: Node后端
sticky: 40
---
# 同源策略

（Same-Origin Policy，简称 SOP）

协议（Protocol）

域名/主机（Host）

端口（Port）

# CORS

>请求: 请求行（第一行）+ 请求头（Headers，多行键值对）+ 空行 +请求体（Body，可选）

`请求行（第一行）: 方法+ URL+ HTTP版本` post/url/api/https1.1

`请求头（Headers，多行键值对）`

```txt
常见：

- Host：目标主机域名
- User-Agent：客户端浏览器 / 设备信息
- Content-Type：请求体数据格式
- Cookie：浏览器自动携带 cookie
- Referer：来源页面
- Accept：能接收什么响应数据
```

`空行` 分隔请求头 和 请求体

`请求体` POST/PUT 携带提交的数据

>简单请求

请求方法：GET、POST 或 HEAD。

请求头：仅包含安全头部，如 Accept、Accept-Language、Content-Language、Content-Type 等。

Content-Type 仅限：text/plain、multipart/form-data、application/x-www-form-urlencoded。

>预检请求（Preflight Request）

浏览器会自动先发起一次 OPTIONS 方法的请求   -----> 否接受该跨域请求

服务器返回 200/204 且允许该请求，浏览器才会发送真实数据请求

```http
Access-Control-Allow-Origin: http://localhost:5173
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400  # 预检结果缓存时间（秒），避免频繁发 OPTIONS 请求
```

HTTP 本身是**无状态协议**

# 认证

| 维度 | Session | JWT Token |
| ---- | ---- | ---- |
| 前端是否手动设头 | ❌ 不用，浏览器自动带 Cookie | ✅ 要设 Authorization |
| 服务端是否存状态 | ✅ 存（内存/Redis） | ❌ 不存 |
| 跨域 | 麻烦，要 credentials + 精确 origin | 简单，普通头即可 |
| 主动失效 | 容易（删 session） | 难（要等过期或维护黑名单） |
| 移动端友好 | 一般 | 好 |
| 扩展性（多台服务器） | 要共享 session 存储 | 天然支持 |



cookie

token JWT 和 session 怎么选？


```http
cookie: {
    httpOnly: true,      // 禁止 JS 读取，防 XSS
    secure: false,       // 生产环境 https 时设 true
    maxAge: 1000 *60* 60 * 24, // 1 天
    sameSite: 'lax',     // 防 CSRF
  }
```

防 XSS


防 CSRF


代理

# Express 中间件

cors

中间件的执行顺序

洋葱模型

MySQL vs MariaDB

索引

# 连接池 字段

复用连接，避免每次查询都重新经历 TCP 握手和 MySQL 认证的开销

connectionLimit: 10 —— 连接池的最大连接数

enableKeepAlive: true —— TCP 保活开关

keepAliveInitialDelay: 0 —— 保活探测的启动延迟

waitForConnections（默认 true）

SQL 注入防范

事务

bcrypt 自带 salt，防彩虹表
