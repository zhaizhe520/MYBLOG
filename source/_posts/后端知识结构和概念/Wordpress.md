---
title: 配置WordPress
date: 2026-04-09 17:03:38
tags:
categories:
  - WordPress
---
# wordpress

今天用學用wordpress
本地开发 WordPress 最简架构（后端标配）

1. Nginx：网站的「大门 + 快递员」
本质：高性能 Web 服务器 / 反向代理服务器
作用：
接收你浏览器发的请求（比如访问 localhost）
把静态资源（HTML、CSS、JS、图片）直接返回给浏览器
把动态请求（.php 文件）转发给 PHP 处理
做负载均衡、缓存、安全防护等
对你来说：就是 WordPress 网站的「入口」，所有访问都要经过它

2. MySQL 8.0：网站的「仓库 + 账本」
本质：关系型数据库
作用：
存储 WordPress 所有数据：文章、页面、用户、配置、评论等
用表结构存数据，PHP 来读写
保证数据的持久化、安全、高效查询
对你来说：就是 WordPress 的「数据库」，存你写的所有内容和配置

3. PHP 8.x：网站的「大脑 + 处理器」
本质：服务器端脚本语言
作用：
解析 WordPress 的 PHP 代码，动态生成 HTML 页面
连接 MySQL 数据库，读写数据
处理业务逻辑（比如用户登录、文章发布、自定义功能）
把处理结果返回给 Nginx，再给浏览器
对你来说：就是 WordPress 的「运行引擎」，没有它 PHP 代码跑不起来
🚀 三者的协作流程（你一看就懂）
你在浏览器输入 localhost → 发送请求给 Nginx
Nginx 发现是 .php 请求 → 转发给 PHP 处理
PHP 执行 WordPress 代码 → 连接 MySQL 读取文章数据
PHP 把数据拼成完整的 HTML → 返回给 Nginx
Nginx 把 HTML 发给你的浏览器 → 你看到网站页面

🎯 为什么必须这三个？
缺 Nginx：没有入口，浏览器访问不了网站
缺 MySQL：没有数据库，WordPress 存不了数据，直接报错
缺 PHP：没有引擎，WordPress 代码跑不起来，页面空白
这三个就是 WordPress 运行的最低要求，也是后端开发 Web 项目的标准环境。

# 進入庫
先切换到 MySQL 安装的 bin 目录：
bash
运行
cd C:\Program Files\MySQL\MySQL Server 8.0\bin
再执行连接命令：
bash
运行
mysql -u root -p
输入密码，进入命令行。


# 执行建库 SQL
1. 执行建库 SQL（在 mysql> 提示符后输入）
sql
CREATE DATABASE wp_local CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
回车执行，看到 Query OK, 1 row affected 就代表创建成功。
这个 wp_local 就是后续安装 WordPress 要用的数据库。

2. 验证数据库（可选）
sql
SHOW DATABASES;
执行后会列出所有数据库，能看到 wp_local 就说明没问题。

3. 退出 MySQL
sql
exit;
回车即可回到 CMD 命令行，接下来就可以配置 Nginx + PHP，启动服务安装 WordPress 了。


# PHP 环境配置（关键步骤）
1. 进入 PHP 解压目录（比如 D:\env\php-8.5.5-nts）
2. 复制配置文件
把 php.ini-development 复制一份，重命名为 php.ini，用编辑器打开，修改以下配置：
ini
; 1. 设置扩展目录（改成你自己的 PHP 路径）
extension_dir = "D:\env\php-8.5.5-nts\ext"

; 2. 开启 WordPress 必需的扩展（去掉前面的分号 `;`）
extension=mysqli
extension=pdo_mysql
extension=curl
extension=gd
extension=mbstring
extension=fileinfo
curl：网络请求
fileinfo：文件信息处理
gd：图片处理
mbstring：多字节字符串（中文支持）
mysqli / pdo_mysql：MySQL 数据库连接

; 3. 设置时区（避免 WordPress 时间错误）
date.timezone = Asia/Shanghai
除了时区，cgi.fix_pathinfo=0 也必须配置，否则 Nginx 会存在路径遍历安全风险，同时可能导致 WordPress 伪静态失效、页面 404。
3. 启动 PHP-FPM（CMD 执行）
bash
运行
cd D:\env\php-8.5.5-nts
php-cgi.exe -b 127.0.0.1:9000
这个窗口不要关，关了 PHP 服务就停了
后续可以做成开机自启服务，先本地测试用这个方式

# Nginx 完整配置

Nginx 完整配置文件（直接替换）
1. 找到 Nginx 配置文件
路径：D:\PhpStudy\nginx-1.28.3\conf\nginx.conf（你的 Nginx 解压目录）

2. 完全替换成下面的配置（注意修改 WordPress 路径）
nginx
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        # 👇 这里改成你自己的 WordPress 解压目录！
        root   D:\PhpStudy\wordpress;
        index  index.php index.html;

        # WordPress 伪静态规则（必须，否则文章页 404）
        location / {
            try_files $uri $uri/ /index.php?$args;
        }

        # 转发 PHP 请求给 PHP-CGI（9000 端口，和 PHP 启动命令一致）
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $realpath_root$fastcgi_script_name;
            include        fastcgi.conf;
        }

        # 禁止访问隐藏文件（安全配置）
        location ~ /\. {
            deny all;
        }
    }
}

3. 关键修改说明
只需要改 root D:\PhpStudy\wordpress; 这一行，换成你自己的 WordPress 文件夹路径
其他配置完全不用改，直接保存即可
二、启动 Nginx（CMD 操作）
1. 先关闭旧 Nginx 进程（如果之前启动过）
bash
运行
taskkill /F /IM nginx.exe
2. 进入 Nginx 目录，启动服务
bash
运行
cd D:\PhpStudy\nginx-1.28.3
start nginx.exe
启动后无任何提示就是成功，Nginx 会后台运行，不用保持 CMD 窗口打开
验证：浏览器访问 http://localhost，如果出现 WordPress 安装页，说明 Nginx 配置生效
三、WordPress 浏览器安装步骤（5 分钟完成）
1. 访问安装页
打开浏览器，输入 http://localhost，自动跳转到 WordPress 安装引导页
选择语言：简体中文 → 点击「继续」
2. 数据库配置（关键，填错连不上）
表格
配置项	填写内容
数据库名	wp_local（之前 MySQL 创建的数据库）
用户名	root（MySQL 超级管理员账号）
密码	你之前设置的 MySQL root 密码
数据库主机	localhost（默认，不用改）
表前缀	wp_（默认，不用改）
点击「提交」→「运行安装程序」
3. 网站信息配置
站点标题：你的网站名称（比如「我的 ACG 博客」）
用户名：WordPress 后台管理员账号（自己设，比如 admin）
密码：后台管理员密码（自己设，务必记住！）
你的邮箱：本地开发可随便填，比如 test@test.com
勾选「建议搜索引擎不索引本站点」（本地环境用）
点击「安装 WordPress」→ 完成后点击「登录」，进入后台


# 第二次配置wordpress

行第一次卡主了，要不再來一次？

# 一.安裝準備

1. Nginx（Web 服务器）
去官网下载：http://nginx.org/en/download.html
下载：nginx/Windows‑xxx.zip解压到：XXXXXX
2. PHP（运行 WordPress 用）
下载：https://windows.php.net/download/
选：Zip → x64 → Non Thread Safe解压到：XXXXXXXX
3. MySQL（数据库）
下载：https://dev.mysql.com/downloads/mysql/
选：Windows (x86, 64-bit), ZIP Archive解压到：XXXX
4.wordpress下載
https://cn.wordpress.org/latest-zh_CN.zip

# 二.開始配置
## MySQL
配置环节（重点）：
端口保持默认 3306，不要修改。
身份验证方式选择 「Use Legacy Authentication Method」（兼容 WordPress，避免连接报错）。
设置 root 账户密码，务必记住，后续安装 WordPress 必须用。
勾选「Start the MySQL Server at System Startup」，设为开机自启，不用每次手动启动。
验证安装：
安装完成后，打开 CMD 输入：
bash
运行
mysql -u root -p
输入你设置的密码，能进入 MySQL 命令行，就说明安装成功。
创建 WordPress 数据库：
进入 MySQL 后执行：
sql
CREATE DATABASE wp_local CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
这个 wp_local 就是后续安装 WordPress 要用的数据库。
![](/img/bg/MySQL.png)

## PHP配置
1. 进入 PHP 目录
2. 复制配置文件
把 php.ini-development 复制一份，重命名为 php.ini，用编辑器打开，修改以下配置：
ini
设置扩展目录（改成你自己的 PHP 路径）
extension_dir = "D:\env\php-8.5.5-nts\ext"XXXXX extension_dir ="\ext"
开启 WordPress 必需的扩展（去掉前面的分号 `;`）
extension=mysqli 
extension=pdo_mysql
extension=curl  网络请求
extension=gd    图片处理
extension=mbstring   多字节字符串（中文支持）
extension=fileinfo  文件信息处理
curl：网络请求
fileinfo：文件信息处理
gd：图片处理
mbstring：多字节字符串（中文支持）
mysqli / pdo_mysql：MySQL 数据库连接
![](/img/bg/PHP.png)

## Nginx配置
***注意：要把wordpress解壓到Nginx的html文件夾裡面***
 修改 Nginx 配置
打开：****\nginx\conf\nginx.conf
找到 server 段，全部替换成下面这段：
nginx
server {
    listen       80;
    server_name  localhost;

    root   html/wordpress;
    index  index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
保存。
![](/img/bg/NIGHT.png)


## 三.終端打開
cd *******\php
php-cgi.exe -b 127.0.0.1:9000
不能刪除

cd*******\nginx
nginx.exe
不能刪除

打開http://localhost/wp-admin
![](/img/bg/OVER.png)



# 一、先给你讲清楚：什么是前后端分离（超简单）
WordPress = 后端 / 数据中心
存文章
存图片
存用户
自动生成 API 接口（不用你写）
Vue3 = 前端 / 展示页面
做界面
做交互
做样式
通过 API 去 WordPress 拿数据
它们的关系：
Vue ← API ← WordPress
# 二、你本地具备的条件（全部满足）
✅ 本地 WordPress 能跑（你已经登录后台了）
✅ Vue3 项目能跑、能打包
✅ 数据库 MySQL 你已经会进了
✅ 你只差：Vue 调用 WP API
1. 找到 WordPress 自带的 API 地址
不用插件、不用配置自带接口例如：
```
plaintext
http://localhost/你的WP地址/wp-json/wp/v2/posts
打开就是文章列表 JSON
```
2. Vue 安装 axios（发送请求）
```
plaintext
npm install axios
```
3. Vue 里写 3 行代码请求 WP 数据
js
axios.get('http://localhost/.../posts').then(res => {
  console.log(res.data)
})catch(error){
  console.log("抓取失敗")
}
4. 把数据渲染到 Vue 页面
```
html
预览
<div v-for="item in list" :key="item.id">
  {{ item.title.rendered }}
</div>
```


# 什麼是axios

簡單來說，Axios 是一個基於 Promise 的 HTTP 客戶端，專門用來在瀏覽器（Frontend）和 Node.js（Backend）環境中發送網路請求。

如果你曾經被原生 JavaScript 那囉唆的 XMLHttpRequest 搞得頭大，或者覺得 fetch 的某些功能（如自動轉換 JSON）還不夠便利，那 Axios 就像是你的「網路請求懶人包」。

🚀 為什麼大家愛用 Axios？
Axios 之所以成為開發者的首選，主要有以下幾個核心特性：

基於 Promise： 它完全支持現代的 async/await 語法，讓非同步代碼看起來像同步一樣整潔。

同構性 (Isomorphic)： 同一套代碼可以運行在瀏覽器端，也可以運行在 Node.js 伺服器端。

自動轉換 JSON： 發送請求時它會幫你把物件轉成 JSON，收到回應時也會自動解析成 JavaScript 物件，省去手動 .json() 的步驟。

攔截器 (Interceptors)： 這是它的殺手鐧。你可以在請求發出前（例如統一加上 Auth Token）或回應回來後（例如統一處理 401 錯誤）進行攔截處理。

請求取消： 內建機制可以讓你取消尚未完成的請求，避免浪費資源。

客戶端防禦 XSRF： 內建了一些基本的安全性保護。

----------------------------------------
## 簡單的git
```
import axios from 'axios';

async function getUserData() {
  try {
    // 不需要手動轉 JSON，response.data 直接就是結果
    const response = await axios.get('https://api.example.com/user/123');
    console.log(response.data);
  } catch (error) {
    // 只要 API 回傳 4xx 或 5xx，都會直接進到這裡
    console.error('抓取失敗：', error.message);
  }
}
```

# 🎯總結
Axios 就是一個更聰明、功能更完整的網路請求工具。對於小型專案，原生 fetch 可能就夠了；但如果你在開發中大型應用，需要統一處理 Token、錯誤提示或複雜的 API 邏輯，Axios 會幫你省下大量的開發時間。

# 推荐插件：All-in-One WP Migration


在你现在的 WordPress 后台 → 插件 → 安装 → 搜 All-in-One WP Migration


启用后，点导出 → 直接导出成一个 .wpress 单文件
（这个文件就是整站备份：文章、设置、图片、插件、数据库全在里面）


新服务器 / 新电脑搭建的空白 WP：

同样装这个插件 → 导入这个 .wpress 文件 → 一键还原

不用单独导 SQL，不用分开传文件