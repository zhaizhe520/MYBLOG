---
title: 后端框架
date: 2026-09-12 10:40:19
tags: 后端框架
excerpt: 后端框架
categories:
    - 后端
---
| 语言生态 | 核心代表 | 生态风格 | 最佳场景 |
|---|---|---|---|
| Node.js | Express, Fastify, NestJS, Hono | 迭代快、碎片化严重、轻量非阻塞、全栈/边缘计算优势明显 | 全栈开发、BFF 层、高并发 I/O、实时应用 |
| Java | Spring Boot, Quarkus, Netty | 高度标准化、企业级规范、强类型、长生命周期 | 金融级后台、复杂企业级微服务系统、大数据处理 |
| Python | Django, Flask, FastAPI | 快速交付、语法优雅、人工智能/数据科学无缝集成 | AI/数据分析后端 API、MVP 快速验证、中小型 Web 系统 |
| Go(Golang) | Gin, Echo, Fiber, Beego | 编译型、极简高效、低内存占用、原生高并发、部署简单 | 高并发网关、微服务、容器云原生、中间件、高性能接口服务 |
| Rust | Axum, Rocket, Actix-web | 极致性能、内存安全、无GC、高稳定性、超低延迟 | 底层基础设施、高性能核心服务、区块链、高可靠低延迟系统 |



```text
┌──────────────────────────────────────────┐
                        │      JavaScript / TypeScript 后端生态      │
                        └────────────────────┬─────────────────────┘
                                             │
      ┌──────────────────────┬───────────────┴───────────────┬──────────────────────┐
      ▼                      ▼                               ▼                      ▼
【1. 底层 / 极简路由层】    【2. 企业级 / 架构驱动】        【3. 全栈 / SSR 服务端】    【4. 跨运行环境 / 边缘】
 (微服务/高性能 API)       (控制反转/依赖注入/规范)        (前端驱动/前后端同构)       (Cloudflare/Deno/Bun)
 ──────────────────        ────────────────────────        ─────────────────────       ──────────────────────
  • Express                 • NestJS (默认Express/Fastify)  • Next.js (React)           • Hono
  • Fastify                 • Midway.js (阿里开源)          • Nuxt.js (Vue)             • Elysia.js (Bun)
  • Koa                     • Sails.js                      • SvelteKit                 • Feathers.js
  • h3 (UnJS)               • LoopBack                      • Remix (React Router)
  • hapi
```


<details>
<summary>Express</summary>

封装Node.js 原生 `http 模块`之上封装的一层轻量级工具库

核心功能仅包含`路由机制`与`中间件架构`

<details>
<summary>中间件</summary>

# 中间件 (Middleware)： Express 的灵魂所在

所有请求在到达最终路由处理逻辑前，都会按顺序通过一系列中间件函数。

每个中间件均可接收 (req, res, next) 三个参数，用于解析请求体、验证身份、记录日志或捕获错误。
<details>
<summary>请求数据解析类</summary>

```js
// 全局应用解析器
app.use(express.json());
app.post('/api/user', (req, res) => {
// 输出解析后的 JavaScript 对象：{ name: 'Alice', age: 25 }
  console.log(req.body);
});
```

```js
//express.urlencoded()（内置）：解析表单数据（application/x-www-form-urlencoded）。
app.use(express.urlencoded({ extended: true }));
```

```js
//multer：处理文件上传（multipart/form-data）。
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

// 仅针对单文件上传接口挂载
app.post('/profile', upload.single('avatar'), (req, res) => {
  console.log(req.file); // 接收文件信息
  res.send('上传成功');
});
```

```js
//cookie-parser：解析客户端传来的 Cookie 并挂载到 req.cookies 上。
const cookieParser = require('cookie-parser');
app.use(cookieParser('secret_key'));
```

</details>

<details>
<summary>安全防护类</summary>

```js
//helmet：修改 HTTP 响应头以提升安全性（如防御 XSS、防点击劫持等）。
const helmet = require('helmet');
app.use(helmet()); // 一键开启多项标准安全防护
```

```js
//cors：开启跨域资源共享，允许其他域名的前端访问 API。
const cors = require('cors');
app.use(cors({ origin: 'https://your-frontend.com' }));
```

```js
//express-rate-limit：限流中间件，防止 IP 恶意刷接口或 DDoS 攻击。
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 分钟
  max: 100 // 每个 IP 最多发 100 个请求
});
app.use(limiter);
```

</details>

<details>
<summary>日志与性能监控类</summary>

```js
//morgan：HTTP 请求日志收集器，自动打印终端日志。
const morgan = require('morgan');
app.use(morgan('dev')); // 打印简短格式的彩图日志
```

```js
//compression：响应体 Gzip 压缩，减少网络传输体积。
const compression = require('compression');
app.use(compression());
```
</details>

<details>
<summary>静态资源与认证类</summary>

```js
//express.static()（内置）：直接对外托管静态文件（如图片、前端构建产物）。
app.use('/static', express.static('public')); 
// 访问 http://localhost:3000/static/logo.png
```

```js
//express-session：服务端 Session 管理。

const session = require('express-session');
app.use(session({
  secret: 'keyboard cat',
  resave: false,
  saveUninitialized: true,
  cookie: { maxAge: 60000 }
}));
```

</details>

<details>
<summary>自定义认证中间件</summary>

```js
// 自定义 JWT 校验中间件
const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];

  if (!token) return res.status(401).json({ error: '未提供 Token' });

  jwt.verify(token, process.env.ACCESS_TOKEN_SECRET, (err, user) => {
    if (err) return res.status(403).json({ error: 'Token 无效或过期' });
    req.user = user; // 将解码后的用户信息挂载到 req
    next(); // 放行
  });
};

// 局部应用到敏感路由
app.get('/api/dashboard', authenticateToken, (req, res) => {
  res.json({ message: `欢迎用户 ${req.user.id}` });
});
```

</details>

</details>

</details>

</details>

<details>
<summary>Fastify</summary>
官方网址:[https://fastify.dev/]

多了个sheame校验 重写了express的路由机制






</details>

<details>
<summary>Nuxt.js</summary>

</details>

<details>
<summary>Next.js</summary>

</details>

<details>
<summary>NestJS</summary>

</details>

<details>
<summary>koa</summary>

</details>
