---
title: 线上部署server.js
date: 2026-05-23 17:55:43
tags: 部署后端
excerpt: 部署后端
categories: 
    - 全栈
---
# 具体流程
第一步：服务器装 Node.js
远程桌面登上 xxxx，终端敲：


node -v
没装的话，去 https://nodejs.org 下载 LTS 版，Windows 安装包双击。装完再敲 node -v 确认。

linux:
```
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```


第二步：上传 server.js + 装包
在服务器上建个文件夹，比如 C:\backend，把本地的 server.js 拖进去。然后在服务器终端：


cd C:\backend
npm init -y
npm install express mysql2 cors
同时改 host——server.js 里 host: '110.42.248.8' 改成 host: '127.0.0.1'。数据库和后端在同一台机器上，走内环，不绕公网。

第三步：安全组开 3000 端口
在云服务器控制台加一条：

描述	来源	协议	端口	动作
Node后端	全部IPv4地址	TCP	3000	允许
第四步：启动 + 验证

# 服务器终端
node server.js

浏览器打开 http://110.42.248.8:3000/api/companies，看到 JSON 就通了。

然后在你本地项目新建 .env 文件：


VITE_API_BASE=http://110.42.248.8:3000

# .env 文件

.env 文件你放那，Vite 启动时会悄悄读它。但不是所有变量都传进来——只有以 VITE_ 开头的才会被暴露给前端代码。这是安全设计：


.env 里写的                    Vite 处理后                前端能读到
VITE_API_BASE=xxx    ──→    暴露给 import.meta.env    ──→  能
DB_PASSWORD=xxx      ──→    过滤掉，不暴露            ──→  不能
如果没有 VITE_ 这个前缀过滤，你写在 .env 里的数据库密码就会被前端打包进去，用户打开浏览器看源码就能看到。




# 本地没问题——是服务器上 node 进程死了。SSH 终端一关，node server.js 就跟着停了。

nvm 只在安装时那个会话里有效，新会话里没加载。先激活 nvm：
source /root/.nvm/nvm.sh
node server.js &

如果每次都要手动 source 嫌麻烦，加到 ~/.bashrc 里一步到位：

echo 'source /root/.nvm/nvm.sh' >> ~/.bashrc

装 Node.js 的工具——Node Version Manager。  用的pm2

~/.bashrc 是每次登录自动执行的文件，加那一行就是让 nvm 自动激活，以后不用手动 source。

# 那挺好——& 让它在后台跑，关终端也不死。

但有一个小提醒：服务器重启的话，node 进程就没了，得再手动起一次。PM2 能帮你自动拉起来，不过现在够用了，等真重启了再说。

