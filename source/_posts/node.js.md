---
title: Node.js介紹與安裝
date: 2026-04-16 17:54:43
tags:
categories:
  - node.js
---
# Node.js 详细介绍（）

## 1. 官方定义

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时（runtime）让 JavaScript 可以脱离浏览器，在服务器 / 电脑本地直接运行
--------------------------------

## 2. 核心本质

浏览器里的 JS：只能操作网页、DOM
Node.js 里的 JS：可以操作文件、网络、数据库、启动服务
它不是：
不是编程语言（语言还是 JS）
不是框架
不是库
不是后端语言（但可以用来写后端）
--------------------------------------

## 3. 为什么能运行 JS？

因为内置了 Chrome V8 引擎，就是谷歌浏览器用来解析 JS 的引擎
--------------------------------------

## 4. 两大自带神器

npm（包管理器）
用来下载第三方库：Vue、React、Axios、jQuery 等
命令：npm install 包名
npx
直接运行某个包
比如快速创建 Vue 项目：npm create vue@latest
-------------------------------------

## 5. 能做什么？

前端开发相关（你学 Vue 会用到的）
启动本地开发服务（npm run dev）
打包编译项目（npm run build）
处理 ES6+、TypeScript、Sass、.vue 文件
热更新、代码压缩、优化
后端开发
写接口 API
操作数据库（MySQL、MongoDB）
做网站服务器、聊天室、小程序后端
工具脚本
批量重命名文件
爬取网页数据
自动化构建
-------------------------------------

## 6. 特点

非阻塞、异步 I/O
高并发性能好
前端后端统一用 JS
生态极庞大（npm 包世界第一多）
------------------------------------

## 7. 和 Vue 的关系

Vue 是前端框架，运行在浏览器
Node.js 是运行环境，用来开发、构建 Vue 项目
浏览器里运行 Vue 不需要 Node.js
开发大型 Vue 项目必须用 Node.js
-------------------------------------

# 最简总结

Node.js = 让 JS 能在电脑本地跑的环境 + 包管理工具前端工程化（Vue/React）离不开它，但写简单 HTML 页面可以完全不用。

# 安裝

1. 下载 Node.js
打开官网：<https://nodejs.org/>
直接下载左边的 LTS 版本（稳定版）

------------------------
1. 安装（全程下一步）
打开下载好的文件一路 Next → Next → Install
不需要改任何设置！不需要勾乱七八糟的东西！

------------------------------
1. 检查是否安装成功
按 Win + R → 输入 cmd → 回车

```
在黑色窗口里输入：
bash
运行
node -v
bash
运行
npm -v

如果出现版本号，比如：
plaintext
v20.18.0
10.8.1
```

✅ 恭喜！Node.js 环境安装成功！
试试你的第一个命令（）
你可以先运行一个简单的命令，感受一下：

```
bash
运行
node -e "console.log('Hello, Node.js!')"
如果终端输出了 Hello, Node.js!，说明 Node.js 能正常运行 JavaScript 代码了！
```

# 第一个vue项目

1. 创建 Vue 项目（官方命令，一键创建）
打开你的终端（CMD/PowerShell/VS Code 终端都可以），输入下面的命令：

```
bash
运行
npm create vue@latest my-first-vue-project
这里 my-first-vue-project 是项目名，你可以改成自己喜欢的名字，比如 vue-study
接下来会有几个选项，全部直接回车就行（默认配置最省事）
```

2. 进入项目文件夹

```
bash
运行
cd my-first-vue-project （你的項目名字文件夾進入）
```

3. 安装依赖（项目需要的所有工具包）

```
bash
运行
npm install
```

4. 启动开发服务器（跑起来！）

```
bash
运行
npm run dev
```

5. 项目结构简单说明（）

```
plaintext
my-first-vue-project/
├── src/
│   ├── App.vue          # 主组件
│   ├── main.js          # 入口文件
│   └── components/      # 放你的 Vue 组件（比如按钮、卡片）
├── index.html           # 页面入口
└── package.json         # 项目配置和依赖
```

6. 怎么开始写你的第一个 Vue 代码？
打开 src/App.vue，把默认内容删掉開始寫

# 訪問 每次打開

```
cd my-first-vue-project（你的項目名字文件夾進入）
npm run dev  （執行項目）
http://localhost:5173/
```

# 項目打包

進入項目
`npm run build`

## 打包之後可能出現問題

直接双击 index.html 时，浏览器会以 file:// 协议打开，很多现代浏览器出于安全限制，会阻止 JS、CSS 等资源加载，导致页面空白。
用本地服务器（Live Server / Python 服务）打开时，是 http:// 协议，浏览器没有安全限制，资源都能正常加载。

# 打包之後會給你壓縮

为什么打包后看不到你的 Vue 代码？
因为 npm run build = 编译 + 压缩

```
它把你写的：
.vue 文件
<template>
<script>
所有变量、函数
全部变成了浏览器能看懂的：
压缩后的 js
压缩后的 css
index.html
```

linux:

```
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```
