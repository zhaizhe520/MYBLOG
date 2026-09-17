---
title: Ktor
date: 2026-06-08 17:04:13
tags: Ktor
excerpt: 后端框架
categories:
    - Android
---

前端的网络请求库（相当于 Android 版的 Axios）。

Ktor Client（网络请求客户端）。

🧭 Android + Nginx + 后端的架构长什么样？它和 Vue 的套路几乎一模一样。你的 server.js（Node.js 后端）现在换成 Ktor（打包出来的 .jar 文件） 放在服务器上运行，Nginx 依然在最外层做反向代理。

Vue 的做法：浏览器访问xxxx Nginx 转发到 localhost:3000 (Node.js)。


Android 的做法：手机 App 内部发起网络请求 x Nginx 接收x 


转发到服务器内部的 localhost:8080 (Ktor 后端)。


唯一的区别：Vue 打包出来的是静态网页（HTML/JS），需要 Nginx 来托管静态文件。

而 Android 打包出来的是一个 app-release.apk 安装包。用户把 APK 下载到手机上安装，App 直接通过网络空中访问你的 Nginx 服务器。


用 Android Studio 导入 Ktor 项目
把你在 start.ktor.io 下载的解压包放在电脑里。

打开 Android Studio，点击 File -> Open (或者在欢迎界面点击 Open)。

选择你解压的 Ktor 项目根文件夹（注意：选择带有 build.gradle.kts 的那个总文件夹）。

Android Studio 会开始在底部加载和同步 Gradle（第一次可能需要几分钟去下载 Ktor 的依赖包，耐心等它走完进度条）。