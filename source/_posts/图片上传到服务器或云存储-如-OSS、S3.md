---
title: 图片上传到服务器或云存储(如 OSS、S3)
date: 2026-05-28 17:58:56
tags: 图床
categories :
    - 全栈
---
存图片的 URL 路径（推荐）：也就是把图片上传到服务器或云存储（如 OSS、S3），数据库里只存一个字符串类型的链接。

如果你只是想找个地方存图片拿来测试，甚至不需要去碰大厂的 OSS。



你可以了解一下 “图床”（比如聚合图床、或者是用 GitHub/Gitee 配合 PicGo 工具搭建免费图床）


连信用卡或实名认证的复杂步骤都省了！

`https://github.com/Molunerfinn/PicGo`



2种办法

# 数据库只存“相对路径”（最推荐，一劳永逸）

前端（Vue 3）动态拼接：


# Nginx 反向代理。
```
server {
    listen       80;
    server_name  110.42.248.8; # 以后换成你的域名

    # ----------------------------------------------------
    # 1. 静态托管：处理 Vue 3 前端路由
    # ----------------------------------------------------
    location / {
        root   /var/www/vue3-project/dist; # 你 Vue 项目 build 打包后的路径
        index  index.html index.htm;
        try_files $uri $uri/ /index.html; # 解决 Vue Router 用 history 模式下刷新 404 的问题
    }

    # ----------------------------------------------------
    # 2. 反向代理：把前端 API 请求转发给 WordPress (8080)
    # ----------------------------------------------------
    location /api/ {
        # 当 Vue 3 请求 /api/v1/posts 时，Nginx 会转发到 http://127.0.0.1:8080/api/v1/posts
        proxy_pass http://127.0.0.1:8080/; 
        
        # 传递真实的客户端信息给 WordPress，否则 WP 记录的 IP 全是 127.0.0.1
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # ----------------------------------------------------
    # 3. 反向代理高级玩：图片防改库劫持（解决你刚才的 GitHub 图片痛点）
    # ----------------------------------------------------
    location /medias/ {
        # 数据库里存 /medias/yuzu-soft/ca.png
        # 浏览器访问 http://110.42.248.8/medias/yuzu-soft/ca.png
        # Nginx 替你去 GitHub 抓取：https://cdn.jsdelivr.net/gh/zhaizhe520/my-img-bed@main/后端的相对路径.图片格式
        
        proxy_pass https://cdn.jsdelivr.net/gh/zhaizhe520/my-img-bed@main/;
        
        # 伪装请求头，让 jsdelivr 以为是正常浏览器访问
        proxy_set_header Host cdn.jsdelivr.net;
        proxy_set_header User-Agent "Mozilla/5.0 (Windows NT 10.0; Win64; x64)";
        
        # 开启缓存（可选）：让 Nginx 把抓过来的图片在服务器存一份，下次别人看就不用去 GitHub 抓了，速度飞快
        expires 30d;
    }
}
```


Kebab Case 烤肉串命名法）

驼峰式命名法

