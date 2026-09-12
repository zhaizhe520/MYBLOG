# 先搞懂：Hexo + GitHub 博客是什么？

Hexo：本地写博客的工具（用 Markdown 写）
GitHub：存放博客网站的免费服务器
Markdown：写文章的极简排版语法（不用调格式，纯文本就能排版）、

# 快速搭建 Hexo + GitHub 博客（5 分钟）

1. 必备环境（先安装）
安装 Node.js（选长期稳定版）
安装 Git
2. 安装 Hexo
打开命令行（CMD / 终端），输入：
bash
运行
# 安装 Hexo
npm install -g hexo-cli

# 查看是否安装成功
hexo -v
3. 初始化博客文件夹
bash
运行
# 创建文件夹（名字随便取）
hexo init myblog
cd myblog

# 安装依赖
npm install
4. 本地预览博客
bash
运行
# 生成静态页面
hexo g

# 启动本地服务器
hexo s
打开浏览器访问：http://localhost:4000 就能看到博客！
5. 关联 GitHub 并部署上线
去 GitHub 新建仓库：用户名.github.io（必须这个名字）
安装部署插件：
bash
运行
npm install hexo-deployer-git --save
打开博客根目录的 _config.yml，拉到最底部修改：
yaml
deploy:
  type: git
  repo: https://github.com/你的用户名/你的用户名.github.io.git
  branch: main
一键部署到 GitHub：
bash
运行
hexo clean && hexo g && hexo d
✅ 上线成功！访问：https://你的用户名.github.io 就是你的博客了。

### 3. 写完文章 → 发布上线
```
```bash
hexo clean && hexo g && hexo d
执行完，你的 GitHub 博客就更新啦！
四、常用 Hexo 命令速查
bash
运行
hexo new "标题"   # 新建文章
hexo g           # 生成网页
hexo s           # 本地预览
hexo d           # 部署到 GitHub
hexo clean       # 清理缓存（更新失败时用）
```

主题官网: `https://github.com/fluid-dev/hexo-theme-fluid`