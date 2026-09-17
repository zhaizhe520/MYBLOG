---
title: Vue3 + Axios + Pinia + Node.js 极简后端 + MySQL
date: 2026-05-18 18:33:42
tags: 全栈
excerpt: 全栈
categories: 
    - 全栈
---
技术栈：Vue3 + Axios + Pinia + Node.js 极简后端 + MySQL



请求进来  →  接待员（Express）  →  办事员（API 路由）  →  仓库（MariaDB）
   ↓              ↓                      ↓                    ↓
浏览器访问    app.use(cors())       app.get('/api/...')    mysql.createConnection()


# nodo.js简易后端

真简易后端接口都不接
```
前端: axios.get('http://localhost:3000/api/hello')
         ↓
后端: app.get('/api/hello', async (req, res) => {
         const result = await db.query('SELECT "你好，全栈冒险者" AS msg')
         res.json(result)
       })
         ↓
前端收到: { msg: "你好，全栈冒险者" }

```
# 第一步：装包。

npm install express mysql2 cors

# 创建 server.js

import express from 'express'
import cors from 'cors' 
import mysql from 'mysql2/promise'
mysql2/promise 这个包帮你在 Node.js 和 MariaDB 之间建立一条持续连接。promise 版本用 await 而不用回调函数，代码读起来更顺畅。


const app = express()   
const PORT = 3000

// 中间件
app.use(cors()) // 允许前端跨域访问
app.use(express.json())  // 能解析前端发来的 JSON 数据


# 第二层：仓库钥匙（数据库连接）

const dbConfig = {
  host: 'xxx.xxx.xxx.x',
  port: 3306,
  user: 'root',
  password: 'xxx',
  database: 'xxx'
}



req = 前端发来的请求（谁在敲门）
res = 你要返回的响应（开门、给东西）
await db.query(...) = 对仓库管理员说"帮我查个东西"
res.json(...) = 把查询结果打包递回去


axios	把 JS 请求翻译成 HTTP 协议

mysql2	把 JS 查询翻译成 MySQL 协议

vue	把 .vue 文件翻译成 HTML/CSS/JS

npm run server	启动后端 API（端口 3000）	调数据库时

# 3306上防火墙

# ssh放行
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '470803581wW!';
FLUSH PRIVILEGES;
解释：

'root'@'%' —— 允许 root 从任何 IP 登录（% = 通配符）
GRANT ALL PRIVILEGES ON *.* —— 授予所有数据库的所有权限
FLUSH PRIVILEGES —— 让权限立刻生效，不重启

<div style="color:pink">更好的做法：建一个专用账号，只给必要权限</div>

-- 创建一个专用用户，只允许从你的 IP 登录
CREATE USER 'noacg_user'@'60.173.217.191' IDENTIFIED BY '你的另一个密码';

-- 只授予 Galgame 这个数据库的增删改查权限（不给 DROP、ALTER 等危险权限）
GRANT SELECT, INSERT, UPDATE, DELETE ON Galgame.* TO 'noacg_user'@'60.173.217.191';

FLUSH PRIVILEGES;
然后 server.js 里改用这个账号：


const dbConfig = {
  host: '110.42.248.8',
  port: 3306,
  user: 'noacg_user',          // ← 专用账号，不是 root
  password: '你的另一个密码',   // ← 独立密码
  database: 'Galgame'
}
三层防护对比
做法	风险
'root'@'%' + 全部权限	高危，任何人都能用 root 密码爆破
'noacg_user'@'%' + 单个库权限	中等，密码泄露也只影响一个库
'noacg_user'@'60.173.217.191' + 单个库权限	推荐，只有你的电脑能连，且只能动一个库

最坏情况外面的人拿到了密码——他也只能在你的 IP 上才能连，而你家里的电脑就是他过不去的那道墙。

##  '专用账户'@'%' + 单个库权限
建专用账号
CREATE USER 'xxx_user'@'%' IDENTIFIED BY '你的强密码';、

第二步：只给 xxx 数据库增删改查权限


GRANT SELECT, INSERT, UPDATE, DELETE ON Galgame.* TO 'noacg_user'@'%';
FLUSH PRIVILEGES; 111