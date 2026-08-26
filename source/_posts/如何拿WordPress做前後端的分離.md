---
title: 如何拿WordPress來寫純HTML
date: 2026-04-10 11:24:50
tags: 如何拿WordPress來寫純HTML
excerpt: 如何拿WordPress來寫純HTML
categories:
    - WordPress
---
# wordpress
目錄下
wp-content下
themes下
創建一個文件夾
裡面.css文件
.php文件
.php文件下面



*******************************
<?php
// 这一行必须留着，什么都不用改
?>

<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>我的網站</title>
</head>
<body>

    <h1>我自己做的網站</h1>

</body>
</html>
***************************
你的文件叫 index.php，不是 index.html
PHP 文件必须以 <?php 开头，否则服务器会报错
WordPress 只识别带 <?php 的主题文件
没有它 → 你的主题会失效、崩溃、甚至打不开


谁这么干啊