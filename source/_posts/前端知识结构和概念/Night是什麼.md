---
title: Nginx是什麼?
date: 2026-04-10 11:06:15
tags:
categories:
  - 為什麼
---
# 介紹
 NGINX（读作 "Engine X"）是一款高性能、开源的 Web 服务器 / 反向代理服务器 / 负载均衡器，是现代网站、API 服务和微服务架构的核心基础设施。
## 作者：俄罗斯程序员 Igor Sysoev（伊戈尔・赛索耶夫）
发布：2004 年公开，基于 BSD 开源协议
本质：C 语言编写的事件驱动、异步非阻塞服务器软件
优势：高并发、低内存、高稳定性、轻量高效

# 最常用 5 大功能
1. 静态 Web 服务器（最基础）
直接托管：HTML、CSS、JS、图片、视频等静态文件
性能远高于 Apache / Tomcat / Node.js 等动态服务器
2. 反向代理（最核心）
架构：用户 → Nginx → 后端服务器（PHP/Java/Python）
作用：隐藏后端真实 IP、统一入口、安全隔离
典型：Nginx 接收请求 → 转发给 PHP-FPM / Tomcat / Node.js 处理
3. 负载均衡（高并发必备）
把流量均匀分发到多台后端服务器
策略：轮询、权重、IP 哈希、最少连接数
解决：防止单点故障、提升整体并发能力
4. 动静分离（网站架构标配）
静态文件（图片 / JS/CSS）：Nginx 直接返回
动态请求（接口 / 登录）：转发给后端应用
效果：大幅减轻后端压力、加速页面加载
5. HTTPS / SSL 终端（安全必备）
Nginx 配置 SSL 证书，处理 HTTPS 加密 / 解密
后端只需跑普通 HTTP，解放后端性能
支持 HTTP/2、HTTP/3（QUIC）等现代协议

# 和PHP的關係
Nginx 本身不执行 PHP，但它是 PHP 项目的黄金搭档：
用户访问 xxx.php
Nginx 收到请求
Nginx 通过 FastCGI 协议 转发给 PHP-FPM 进程
PHP-FPM 执行 PHP 代码、连接数据库
结果返回 Nginx → Nginx 再返回给用户