---
title: 第二次配置wordpress
date: 2026-04-09 21:25:55
tags:
categories:
  - WordPress
---
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