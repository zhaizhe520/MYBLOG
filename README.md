# 个人技术博客

主题：

`node_modules`、`public`、`.deploy_git` 不会提交到仓库，拉取源码后需要重新安装依赖。`node_modules`、`public`、`.deploy_git` 不会提交到仓库，拉取源码后需要重新安装依赖。

**现象**：GitHub 网页能看到 `source`、`themes/fluid` 文件夹，点开里面是空，本地文件完整存在。

**原因 1**：fluid 主题文件夹内部自带独立 `.git`，属于子 git 仓库，外层 git 不会读取子仓库内部文件。

✅解决：删除 `themes/fluid/.git`（**不要删除项目根目录的.git**）

**原因 2**：旧 git 缓存索引残留（曾经手动删除根目录`.git`文件夹，暴力操作导致索引错乱）

✅解决：清除 git 缓存，本地文件不会丢失

```
git rm --cached -r source //清理缓存 .git 删了
git rm --cached -r themes //清理缓存 .git 删了
git add .
git commit -m "提交源码"
git push
```

 Github 网页手动新建 README.md，本地 push 直接冲突报错

# 不传目前fluid主题的方法

子模版 套皮 不传子模版 随意更新主题

# 不传任何主题

到时候自己下



### 三种方案快速小结，方便你写readme
|方案|说明|适合场景|
|---|---|---|
|A 提交完整主题源码|themes全部上传github|新手，修改过主题源码，追求开箱即用|
|B Git Submodule子模块|不上传源码，记录主题仓库地址|不修改主题源码，经常升级主题|
|C 忽略themes文件夹|git完全不管主题，手动下载|不想提交主题，不想学子模块，随便换主题|

>你现在选C，最简单，代价就是主题自己手动维护。
>⚠️提醒：你的 `_config.fluid.yml` 是放在根目录，**这个文件一定要提交git！**，你的主题配置全部保存在这里，换电脑依然保留你的配置。
