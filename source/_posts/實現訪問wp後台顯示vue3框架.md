---
title: 实现wp前端顯示vue3框架
date: 2026-05-05 00:07:47
tags:
excerpt: 前端显示vue3框架
categories:
    - Vue框架
---
# 方案一：Nginx 反向代理分流（工程師思維，最推薦）
這是你提到的方法，直接在 Nginx 決定誰來處理請求。
```
實作方式：
將網域根目錄 / 指向 Vue 的 index.html。

將 /wp-json、/wp-admin、/wp-content 等路徑代理（Proxy）或跳轉至 WordPress 的目錄。

優點：

效能最高：Vue 靜態資源由 Nginx 直接吐給用戶，不經過 PHP。

徹底分離：前端掛了 WordPress 還在，WordPress 壞了前端（靜態頁）還能開。

缺點：

需要 VPS 權限（修改 Nginx Config）。

配置不當容易產生 404（例如 Vue Router 的 History mode 與 WP 路徑衝突）。

```
# 方案二：WordPress 主題化（省事思維，最簡單）

```
把 Vue 當成一個「超級外殼」套在 WordPress 身上。

實作方式：

將 Vue npm run build 出來的檔案丟進 WP 的主題資料夾。

修改主題的 index.php，只留一個 <div id="app"></div> 並載入 Vue 的 JS/CSS。

優點：

SEO 友善：可以利用 WP 的外掛（如 Yoast SEO）處理 Meta Data，再傳給 Vue。

部署簡單：只要能上傳主題就能用，適用於普通的 PHP 虛擬主機。

缺點：

效能損耗：每次訪問都要經過 WP 核心加載，明明是看 Vue 卻啟動了整個 PHP 引擎。
```

# 方案三：子網域完全分離（架構思維，最專業）

```
這是大型專案最常見的做法。

實作方式：

前端：[www.yourdomain.com](https://www.yourdomain.com) (部署在 Vercel / Nginx / CDN)。

後端：api.yourdomain.com (純 WordPress)。

優點：

擴充性強：前後端完全獨立，甚至可以放在不同的伺服器。

管理方便：開發前端時不需要啟動 PHP 環境，只需對接 API。

缺點：

CORS 跨域問題：需要額外處理 WP 的跨域 Header 授權。

SEO 挑戰：需要 Vue 端做 SSR（如 Nuxt.js）或預渲染（Prerender），否則搜尋引擎爬不到內容。

```

# Nginx （nginx -s reload重載配置）
// 直接寫相對路徑即可
這樣瀏覽器會認為請求是發給當前域名的，Nginx 會在背後悄悄把請求轉給 WordPress，完美避開跨域 (CORS) 問題。

-在服務器上git倉庫，再安裝node.js，再npm install,再npm run build。服務器垃圾幹不了

-本地打包dist上傳到服務器

## 服務器部署（本地參考）
ssh 
cd 命令進入 nginx

```
創建nginx->新index.html就是dist裡面的，不是wp裡面的
mkdir -p /www/wwwroot/my-first-vue-project
進入這個文件夾放dist文件
ls -d /xxx/xxx/my-first-vue-project
查看
```
-----------------------

回去找到nginx.conf
cd /www/server/nginx/conf/nginx.conf
修改 Nginx 實現「無頭分流」
vi /www/server/nginx/conf/nginx.conf
```
server {
    listen 80;
    server_name your_domain_or_ip; # 填入你的域名或 IP

    # 1. 處理 Vue 3 前端界面
    location / {
        root /xxx/xxx/my-first-vue-project/dist; # 指向你剛剛上傳的 dist
        index index.html;
        try_files $uri $uri/ /index.html; # 這是 Vue Router 必備設定
    }

    # 2. 處理 WordPress API 請求 (反向代理)
    location /wp-json/ {
        proxy_pass http://127.0.0.1:8080; # 假設 WP 跑在 8080 端口
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # 3. 讓 WordPress 的管理後台與檔案可正常訪問
    location ~ ^/(wp-admin|wp-login\.php|wp-content|wp-includes) {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
    }
}
-----------------------------------------
如果nginx.conf 已經幫你「預留」了擴展空間
請在 SSH 輸入這行指令（這會用 vi 編輯器建立一個新檔案）：
vi /www/server/panel/vhost/nginx/my_project.conf

server {
    listen 80;
    server_name 你的伺服器IP; # 這裡填入你的 IP

    # --- 1. 給 Vue 3 前端用的門面 ---
    location / {
        # 這裡就是你剛才上傳 dist 的地方
        root /acg/noacgroot/my-first-vue-project/dist;
        index index.html;
        # 這是為了讓 Vue 的路由（router）正常運作
        try_files $uri $uri/ /index.html;
    }

    # --- 2. 給 WordPress API 用的後門 ---
    # 當 Vue 需要抓文章數據（請求包含 /wp-json/）時，轉發給 WP
    location /wp-json/ {
        # 這裡先暫定 8080，如果 WP 跑在別的地方我們要再改
        proxy_pass http://127.0.0.1:8080; 
        proxy_set_header Host $host;
    }
}

# 先檢查地圖有沒有畫錯（有沒有打錯字）
/www/server/nginx/sbin/nginx -t

# 如果最後一行出現 "test is successful"，就執行重啟
/www/server/nginx/sbin/nginx -s reload

💡 為什麼要分開建立檔案？
安全：萬一寫錯了，你只需要刪掉 my_project.conf，原本的 WordPress 和 phpMyAdmin 都不會壞掉。

乾淨：你的 nginx.conf 保持原樣，專案設定分門別類。

```

```
# 1. 檢查語法有無錯誤
/www/server/nginx/sbin/nginx -t

# 2. 如果顯示 test is successful，重啟 Nginx
/www/server/nginx/sbin/nginx -s reload

```
----------------------
```
chmod -R 755 /XXX/xxxroot修正權限
```
------------------------------
查到wp端口
netstat -ntlp | grep php

cat /www/server/panel/vhost/nginx/你的域名.conf

改
`
vi /www/server/panel/vhost/nginx/wordpress.local.conf
`

```
listen 8080 default_server;
listen [::]:8080 default_server;
```

<div style="color:pink">8080上防火墻</div>

------------------------------

<div style="color:pink">404 Not Found 解決辦法</div>

1. 檢查 Vue 的 dist 目錄權限
如果 Nginx 沒有權限進入你的資料夾，它會報 404 或 403。
請執行這行指令，把你的專案目錄權限開放給 Nginx 讀取：

Bash
chmod -R 755 /xxx/xxxroot
2. 檢查 root 路徑是否寫對
請再次確認你的 my_project.conf 檔案內容。
執行：cat /www/server/panel/vhost/nginx/my_project.conf

重點檢查這幾行：

`root /xxx/xxxroot/my-first-vue-project/dist;`

👉 請確認你的 index.html 真的就在這個 dist 資料夾裡。

👉 你可以輸入` ls /acg/noacgroot/my-first-vue-project/dist `看看有沒有看到檔案。

`index index.html;`

👉 是否有漏寫這行？

3. 檢查「預設頁面」的衝突
很有可能你的伺服器還有另一個「預設設定檔」佔用了 80 端口。
請執行這個指令，看看除了你的檔案，還有誰在用 80 端口：

```
Bash
grep -r "listen 80" /www/server/panel/vhost/nginx/
如果看到 0.default.conf 或是 wordpress.local.conf 也還在 listen 80：
```

請回去把它們的 80 改成 8080。

因為 Nginx 如果看到兩個 80 端口的設定，它通常會優先讀取「字母順序靠前」的那一個（例如 0.default.conf），而那個檔案裡的路徑可能是錯的，所以噴出 404。

4. 你的 Vue 是用什麼路由模式？
如果你在 Vue 3 專案裡用了 History Mode（網址沒有 # 號的那種），那麼 try_files 這一行絕對不能寫錯：

Nginx
location / {
    ...
    try_files $uri $uri/ /index.html;
}
這行的意思是：如果找不到檔案，就通通丟給 index.html 處理。如果沒寫這行，刷新頁面時一定會 404。
----------------------------------------------

<div style="color:pink">「端口衝突」</div>
改端口

```
# 1. 再次給予資料夾權限 (保險起見)
chmod -R 755 /xxx/xxxroot

# 2. 測試配置是否乾淨了
/www/server/nginx/sbin/nginx -t

# 3. 重新加載
/www/server/nginx/sbin/nginx -s reload

```

只要避開以下幾個「釘子戶」端口，就不會有問題：

22：SSH 連線（如果你改了這個又沒設好，你就連不進伺服器了）。

3306：MySQL 資料庫。

8888 / 888 / 21：通常是寶塔面板、phpMyAdmin 或 FTP 在用的。

你剛才看到的 netstat -ntlp 截圖已經確認了這些重要位置，我們選的 8080、8881、8882 都是安全的「空房間」。

`http://xxxx:8080/wp-admin/`

<div style="color:pink">可能出現的問題</div>
之前進不去是因為少了最後那個 斜槓 /。在 Web 伺服器的邏輯裡，wp-admin 是一個資料夾，如果不加斜槓，Nginx 有時候會試著做一次「修正跳轉」，而這個跳轉往往會因為端口改動（從 80 變成 8080）而失敗。現在手動補上斜槓，就直接定位到了後台。
既然 WordPress 後台穩了，我們現在來確認最後一步：你的 Vue 3 專案與 WordPress 的連動。

1. 不打斜槓（wp-admin）：伺服器以為你在找「檔案」
2. 加了斜槓（wp-admin/）：伺服器知道你在找「房間」

平常我們在 80 端口（預設端口）時，Nginx 的「自動跳轉」功能很聰明，會自動幫你補上斜槓。
當 Nginx 試圖幫你補斜槓並跳轉時，如果配置沒有處理好，它有時會漏掉 :8080 這個端口資訊，把你丟回原本的 http://IP/wp-admin/（80 端口）。

因為 80 端口現在住的是 Vue 專案，Vue 不認得 WordPress 的路徑，所以你就看到了空白或者是錯誤畫面。
手動加上那個 /，等於是直接給了伺服器「精確座標」，讓它跳過那個「可能會出錯的自動跳轉步驟」。這是在處理非標準端口（如 8080）時，最穩妥的做法。
