---
title: Git应用
date: 2026-05-03 22:06:00
tags: Github
excerpt: 項目傳到github庫
categories: 
    - Github
---

# Git是什麼
Git是一款开源的分布式版本控制系统，核心用于追踪文件（尤其是代码）的历史变更，支持多人协作与版本回溯，是现代软件开发与项目管理的标配工具。

# 📌 核心定义
分布式：每位开发者本地都有完整仓库（含全历史），断网也能提交、分支、回退，联网后同步。



版本追踪：每次修改生成唯一快照（SHA-1 哈希），可随时回滚到任意历史版本，对比差异、恢复文件。

分支协作：轻量级分支（如功能分支、修复分支）支持并行开发，完成后合并到主分支，高效协作。

ssh 协议


https 协议


# 檢查

1. 準備工作：.gitignore 檢查
在傳上去之前，絕對不能把 node_modules 傳上去。

檢查項目根目錄是否有 .gitignore 檔案。

確保裡面有 node_modules/ 和 dist/。

2. 在 GitHub 上創建倉庫 (Repository)
登入你的 GitHub。

點擊右上角 「+」 -> New repository。

設定 Repository name（例如：my-vue-wp-project）。

選擇 Public 或 Private（建議先選 Public 以便展示給別人看）。

不要勾選 Initialize with a README（因為你本地已經有項目了）。

點擊 Create repository。

# 上傳

```
# 1. 初始化 Git 倉庫（如果之前沒做過）
git init

# 2. 將所有檔案加入暫存區
git add .

# 3. 提交到本地版本庫
git commit -m "feat: 完成 WordPress API 數據抓取與 UI 優化"

# 4. 分支更名為 main (GitHub 預設分支名)
git branch -M main

# 5. 關聯遠程倉庫 (將下方網址換成 GitHub 給你的那個)
git remote add origin https://github.com/你的用戶名/你的倉庫名.git

# 6. 正式推送到 GitHub
git push -u origin main

```

# 保存和再次上傳

查看庫狀態:`git status`

存儲備份:`git add .`

確認修改:`git commit -m "這裡寫你的修改記錄"`

把代碼推送到 GitHub 遠端倉庫:`git push`

# 撞車解決

1. 先把雲端的更新抓下來並合併

`git pull origin main --rebase`

上傳:`git push origin main`

手動上傳

強制推送:`git push origin main --force`

# 撤回
1. 剛敲完 git add . 想撤回（還沒 commit）
如果你發現有些檔案不想加入暫存區，或者想取消剛才的 add 動作：

指令： git reset

效果： 這會把檔案從暫存區拿出來，但不會刪掉你寫的程式碼。

---------------------------------

2. 剛敲完 git commit 想撤回（還沒 push）
如果你發現 commit 的內容有錯，或是訊息寫錯了：

保留代碼但取消 commit： git reset --soft HEAD~1

這會回到「剛 add 完」的狀態，你的程式碼還在，可以修改後再 commit。

完全不要這次 commit 的代碼： git reset --hard HEAD~1

這會直接抹掉最後一次提交的所有變更，回到上一個版本的狀態。


--------------------------------------

3. 想直接「放棄所有未存檔的修改」
如果你剛才在 Cursor 裡亂改一通，現在想直接回到上次 commit 時乾淨的樣子：

指令： git checkout . 或是 git restore .

效果： 所有的檔案會瞬間恢復到上次存檔的狀態。

# 上传出错

先检查你的远程名称：

`git remote -v `

重命名

`git remote rename 原来名字 新的名字`
确认分支,不支持改分支名字

删除远程的旧分支：
`git push origin --delete 旧名字`


`git branch`


`git branch -m <新名字>`


# 超市清代理

```
git config --global --unset http.proxy   # 清掉 git 用的 HTTP 代理
git config --global --unset https.proxy  # 清掉 git 用的 HTTPS 代理
git config --global --unset http.sslVerify
```

# 合并，解决冲突







git rm --cached server.js	只取消跟踪，保留文件
git rm server.js	取消跟踪 且删除文件

等下次合并分支的时候在说


```
你的电脑（私钥）          GitHub（公钥）
    ↓                        ↓
  ssh-keygen 生成  →  cat 复制  →  Settings 粘贴
    ↓                        ↓
          连接时自动握手验证
          → 对上了 → 推送成功

```
# ssh推送


SSH 是比 HTTPS 更老、更稳的远程通信协议。你刚生成的两把钥匙就放在：

```
C:\Users\Yasina\.ssh\
├── id_rsa       ← 私钥，永远不给人看
└── id_rsa.pub   ← 公钥，可以到处贴
```


内部原理就是你电脑的密钥和 GitHub 上的锁自动加密握手


1. 生成钥匙

ssh-keygen -t rsa -b 4096 -C "你的GitHub邮箱@qq.com" -f ~/.ssh/id_rsa -N ""


2. 查看公钥

cat ~/.ssh/id_rsa.pub
输出长这样：ssh-rsa AAAAB3NzaC1yc2EAAA... 全选复制。

3. 粘贴到 GitHub
浏览器打开 https://github.com/settings/keys → New SSH key → 标题随便写"我的电脑"，Key 里粘贴刚才复制的内容 → Add SSH key。

4. 切远程地址

git remote set-url origin git@github.com:zhaizhe520/项目名字.git
5. 推送

git push