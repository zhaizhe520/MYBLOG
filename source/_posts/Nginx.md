---
title: Nginx
date: 2026-05-28 22:07:46
tags: Nginx
excerpt: Nginx
categories: 
    - 全栈
---
# 静态文件分发（不是反向代理）

如果你在 location 里面看到了 root 或者 alias，这意味着 Nginx 会直接去服务器的硬盘上找文件读给用户。

特点：Nginx 自己就是文件的拥有者，自己读硬盘，不转发给任何人。

# 反向代理（Proxy）

```
location /wp-json/ {
    proxy_pass xxx; 转发给xxxx
}

location /medias/ {
    proxy_pass https://cdn.jsdelivr.net/; # 转发给远端的 GitHub/jsDelivr
}
```

特点：Nginx 自己不产出内容，也不读本地硬盘，它只是一个传话筒（代理人），把请求悄悄发给后面真正提供服务的角色（不论是本地的 8080 端口，还是网络上的第三方服务器），拿到结果后再吐给浏览器。

# 功能性指令（重定向、报错等）

```
location /old-path {
    return 301 https://110.42.248.8/new-path; # 告诉浏览器：“搬家了，快去新地址”
}
```

读读 HTTP 协议的基础（状态码、请求头、缓存机制如 Cache-Control、ETag）。

在写 Nginx 配置时就不是“照葫芦画瓢”，而是知道每一行参数背后在对浏览器施加什么魔法。

性能与安全： 比如你在 Nginx 里开启的 proxy_buffering 和 expires，这就是性能优化的开始。后续可以顺着这个方向去了解 HTTPS 证书配置、防 SQL 注入、防跨站脚本（XSS）等。


去看大项目的“异常处理”（长防御力）


去看人家的“规范和结构”（长工程力）

别怕看报错，那是在给你“送经验”