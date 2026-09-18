---
title: Linux操作系统
date: 2026-09-17 22:37:54
tags: Linux操作系统
excerpt: Linux操作系统
---
# 基本命令

# Linux / 服务器常用命令速查

## 基本命令

| 命令 | 作用 |
| ---- | ---- |
| `ssh root@服务器ip` | 远程连接服务器 |
| `ls` | 列出当前目录文件；`ls -lh` 带大小、时间 |
| `cd /xxx` | 切换目录；`cd ~` 回到家目录 |
| `pwd` | 查看当前所在路径 |
| `mkdir xxx` | 创建文件夹；`mkdir -p /www/typewords` 递归创建多级目录 |
| `rm file` | 删除文件；`rm -r folder` 删除文件夹；⚠️ `rm -rf` 谨慎使用 |
| `cp source dest` | 复制文件；`cp -r` 复制文件夹 |
| `mv old new` | 移动/重命名文件 |
| `tar -zcvf out.tar.gz .output/public` | 打包静态文件为压缩包 |
| `tar -zxvf out.tar.gz` | 解压 gz 压缩包 |

`ls -la` 其实就是 `ls -l -a`：

- `-l`：长格式，显示权限、所有者、大小、时间
- `-a`：显示所有文件，包括 `.` 开头的隐藏文件

常用变体：

- `ls -lah`：大小人类可读，如 KB/MB
- `ls -lht`：按修改时间倒序，最新在前
- `ls -ltr`：按修改时间正序，最旧在前
- `ls -lhS`：按文件大小排序
- `ls -laR`：递归列出子目录
- `ll`：很多系统里是 `ls -l` 的别名，不一定默认有

---

## 1. 文件与目录

- `cd`：回到家目录
- `cd ~`：回到家目录
- `cd -`：回到上一次所在目录
- `cd ..`：上一级
- `cd ../..`：上两级
- `pwd`：显示当前路径
- `pwd -P`：显示物理路径，不显示软链接路径
- `mkdir xxx`：创建目录
- `mkdir -p /www/typewords`：递归创建多级目录
- `rmdir xxx`：删除空目录
- `rm file`：删除文件
- `rm -i file`：删除前询问
- `rm -r folder`：递归删除目录
- `rm -rf folder`：强制递归删除，危险
- `cp source dest`：复制文件
- `cp -r folder1 folder2`：复制目录
- `cp -a source dest`：保留权限、时间、链接等复制
- `cp -v source dest`：显示复制过程
- `mv old new`：移动或重命名
- `mv -v old new`：显示移动过程
- `touch file`：创建空文件或更新时间
- `ln -s /target /link`：创建软链接
- `ln /target /link`：创建硬链接
- `stat file`：查看文件详细状态
- `file file`：查看文件类型
- `basename /a/b.txt`：取文件名 `b.txt`
- `dirname /a/b.txt`：取目录名 `/a`
- `realpath file`：显示绝对路径
- `readlink -f file`：解析软链接最终路径
- `tree -L 2`：树状显示两层目录，需安装 tree
- `du -sh folder`：查看目录总大小
- `du -h --max-depth=1 | sort -h`：查看一级子目录大小并排序
- `df -h`：查看磁盘使用
- `df -i`：查看 inode 使用
- `mktemp`：创建临时文件
- `install -d /path`：创建目录并设权限

---

## 2. 查看、编辑、文本处理

- `cat file`：查看文件全部内容
- `cat -n file`：带行号查看
- `tac file`：倒序查看
- `nl file`：带行号输出
- `head file`：看开头 10 行
- `head -n 20 file`：看开头 20 行
- `tail file`：看末尾 10 行
- `tail -n 50 file`：看末尾 50 行
- `tail -f app.log`：实时跟踪日志
- `tail -F app.log`：日志轮转后继续跟踪
- `less file`：分页查看，`/关键词` 搜索，`q` 退出
- `more file`：简单分页查看
- `wc -l file`：统计行数
- `wc -w file`：统计单词数
- `wc -c file`：统计字节数
- `grep "keyword" file`：搜索关键词
- `grep -i "keyword" file`：忽略大小写
- `grep -n "keyword" file`：显示行号
- `grep -r "keyword" .`：递归搜索
- `grep -rin "keyword" .`：递归、忽略大小写、显示行号
- `grep -v "keyword" file`：反选，不包含关键词的行
- `grep -E "regex" file`：扩展正则
- `grep -A 3 -B 3 "keyword" file`：显示前后 3 行
- `grep -rl "old" .`：只列出包含 old 的文件名
- `zgrep "keyword" file.gz`：搜索压缩日志
- `zcat file.gz`：查看 gz 内容
- `sed -n '1,20p' file`：只打印 1 到 20 行
- `sed -i 's/old/new/g' file`：替换文本
- `sed -i.bak 's/old/new/g' file`：替换并备份
- `awk '{print $1}' file`：打印第一列
- `awk -F',' '{print $2}' file`：按逗号分割打印第二列
- `cut -d: -f1 /etc/passwd`：按冒号切割取第一列
- `sort file`：排序
- `sort -u file`：排序去重
- `sort -h`：按人类可读大小排序
- `uniq -c`：统计连续重复行次数
- `tr 'a-z' 'A-Z'`：小写转大写
- `xargs`：把输入变成命令参数
- `tee file`：同时输出到屏幕和文件
- `diff -u a.txt b.txt`：比较文件
- `patch -p1 < fix.patch`：打补丁
- `jq . file.json`：格式化 JSON
- `vim file`：编辑文件，`i` 插入，`:wq` 保存退出，`:q!` 不保存退出
- `nano file`：简单编辑器，`Ctrl+O` 保存，`Ctrl+X` 退出
- `echo "text"`：输出文本
- `printf "%s\n" "text"`：格式化输出
- `cmd > file`：覆盖输出到文件
- `cmd >> file`：追加输出到文件
- `cmd 2> err.log`：错误输出到文件
- `cmd > out.log 2>&1`：标准输出和错误都到同一文件
- `cmd1 && cmd2`：前一个成功才执行后一个
- `cmd1 || cmd2`：前一个失败才执行后一个
- `cmd1 ; cmd2`：顺序执行
- `$?`：上一条命令退出码，0 表示成功
- `watch -n 2 'df -h'`：每 2 秒执行一次
- `time command`：统计命令耗时

---

## 3. 权限、用户、组

- `chmod 755 file`：设置权限 rwxr-xr-x
- `chmod 644 file`：设置权限 rw-r--r--
- `chmod +x script.sh`：增加执行权限
- `chmod -R 755 folder`：递归设置权限
- `chown user file`：改所有者
- `chown user:group file`：改所有者和组
- `chown -R www-data:www-data /www`：递归改所有者
- `chgrp group file`：改所属组
- `umask`：查看默认权限掩码
- `sudo command`：以 root 执行
- `sudo -i`：切换到 root 交互 shell
- `su -`：切换到 root
- `sudo -l`：查看当前用户可执行的 sudo 命令
- `visudo`：安全编辑 sudoers
- `useradd -m -s /bin/bash user`：创建用户并建家目录
- `adduser user`：交互式创建用户，Debian/Ubuntu
- `passwd user`：设置密码
- `passwd -l user`：锁定用户
- `usermod -aG sudo user`：把用户加入 sudo 组
- `usermod -aG docker user`：把用户加入 docker 组
- `deluser user`：删除用户，Debian/Ubuntu
- `deluser --remove-home user`：删除用户和家目录
- `groupadd dev`：创建组
- `groups user`：查看用户所属组
- `id`：查看当前用户 uid/gid/组
- `id user`：查看指定用户
- `whoami`：当前用户名
- `who`：当前登录用户
- `w`：当前登录用户及正在做什么
- `last`：登录历史
- `lastb`：失败登录历史
- `chage -l user`：查看密码过期信息

---

## 4. 查找与搜索

- `find /path -name "*.log"`：按名字查找
- `find . -type f -name "*.js"`：只找文件
- `find . -type d -name "node_modules"`：只找目录
- `find . -type f -size +100M`：找大于 100M 的文件
- `find . -type f -mtime +7`：找 7 天前修改的文件
- `find . -type f -mtime -1`：找 1 天内修改的文件
- `find . -type f -name "*.log" -delete`：找到并删除，危险
- `find . -type f -name "*.log" -exec rm {} \;`：找到并执行删除
- `find . -type f -perm 644`：按权限查找
- `find / -perm -4000 -type f`：查找 SUID 文件
- `find . -not -path "./node_modules/*"`：排除目录
- `locate file`：快速查找，需 updatedb
- `updatedb`：更新 locate 数据库
- `which command`：查看命令路径
- `whereis command`：查看命令相关路径
- `type command`：查看命令类型，内建/别名/文件

---

## 5. 压缩、解压、传输

- `tar -zcvf out.tar.gz .output/public`：打包并 gzip 压缩
- `tar -zxvf out.tar.gz`：解压 gz 包
- `tar -zxvf out.tar.gz -C /www`：解压到指定目录
- `tar -ztvf out.tar.gz`：查看压缩包内容
- `tar -zcvf out.tar.gz --exclude=node_modules .`：排除目录打包
- `gzip file`：压缩成 file.gz
- `gunzip file.gz`：解压 gz
- `zip -r out.zip folder`：zip 压缩目录
- `unzip out.zip`：解压 zip
- `unzip -l out.zip`：查看 zip 内容
- `unzip out.zip -d /www`：解压到指定目录
- `xz file`：xz 压缩
- `unxz file.xz`：解压 xz
- `scp file root@ip:/www/`：上传文件
- `scp -r folder root@ip:/www/`：上传目录
- `scp -P 2222 file root@ip:/www/`：指定 SSH 端口
- `scp root@ip:/www/file .`：下载文件
- `rsync -avz local/ root@ip:/remote/`：同步目录
- `rsync -avz --delete local/ root@ip:/remote/`：删除远端多余文件后同步，危险
- `rsync -avz -e "ssh -p 2222" local/ root@ip:/remote/`：指定 SSH 端口
- `sftp root@ip`：SFTP 交互传输
- `curl -T file ftp://...`：上传文件
- `rz`：上传文件，需 lrzsz
- `sz file`：下载文件，需 lrzsz
- `ssh-keygen -t ed25519 -C "email"`：生成 SSH 密钥
- `ssh-keygen -t rsa -b 4096`：生成 RSA 4096 密钥
- `ssh-copy-id root@ip`：复制公钥到服务器
- `ssh-add ~/.ssh/id_ed25519`：添加密钥到 agent
- `ssh -i key.pem root@ip`：指定私钥登录
- `ssh -p 2222 root@ip`：指定端口登录
- `ssh -L 8080:localhost:80 root@ip`：本地端口转发
- `ssh -R 8080:localhost:80 root@ip`：远程端口转发
- `ssh -D 1080 root@ip`：动态 socks 代理

---

## 6. 进程、系统、服务

- `ps aux`：查看所有进程
- `ps -ef`：查看所有进程，另一种格式
- `ps aux | grep nginx`：过滤进程
- `ps aux --sort=-%mem | head`：按内存倒序
- `pgrep nginx`：按名字找 PID
- `pkill nginx`：按名字杀进程
- `pkill -f "node app.js"`：按完整命令行杀进程
- `kill PID`：发送终止信号
- `kill -15 PID`：优雅终止
- `kill -9 PID`：强制杀死
- `killall nginx`：按名字杀所有
- `top`：实时进程，`P` 按 CPU，`M` 按内存，`q` 退出
- `htop`：更友好进程查看，需安装
- `free -h`：内存使用
- `uptime`：负载和运行时间
- `vmstat 1`：系统状态
- `iostat -x 1`：磁盘 IO
- `iotop`：磁盘 IO 进程
- `sar`：历史性能
- `nohup command > app.log 2>&1 &`：后台运行并写日志
- `command &`：后台运行
- `jobs`：查看后台任务
- `bg %1`：后台继续
- `fg %1`：调回前台
- `Ctrl+Z`：暂停当前任务
- `screen -S name`：创建 screen
- `screen -r name`：恢复 screen
- `Ctrl+A D`：退出 screen 但保持运行
- `tmux new -s name`：创建 tmux
- `tmux attach -t name`：连接 tmux
- `Ctrl+B D`：退出 tmux 但保持运行
- `systemctl status nginx`：查看服务状态
- `systemctl start nginx`：启动服务
- `systemctl stop nginx`：停止服务
- `systemctl restart nginx`：重启服务
- `systemctl reload nginx`：重载配置
- `systemctl enable nginx`：开机自启
- `systemctl disable nginx`：取消开机自启
- `service nginx status`：旧式服务命令
- `journalctl -u nginx`：查看服务日志
- `journalctl -u nginx -f`：实时查看服务日志
- `journalctl -xe`：查看最近错误
- `journalctl --since "1 hour ago"`：最近一小时日志
- `dmesg -T | tail`：查看内核日志
- `dmesg -w`：实时内核日志
- `lsof -i:80`：查看 80 端口占用
- `lsof -p PID`：查看进程打开文件
- `strace -p PID`：跟踪系统调用
- `crontab -e`：编辑定时任务
- `crontab -l`：列出定时任务
- `crontab -r`：删除所有定时任务，危险
- `at now + 1 hour`：一次性定时任务
- `shutdown -h now`：立刻关机
- `reboot`：重启
- `poweroff`：关机

---

## 7. 网络、端口、下载

- `ip addr`：查看 IP
- `ip a`：简写
- `ip route`：查看路由
- `ip link`：查看网卡
- `hostname -I`：查看本机 IP
- `ping host`：测试连通
- `ping -c 4 host`：ping 4 次
- `curl URL`：请求 URL
- `curl -I URL`：只看响应头
- `curl -L URL`：跟随重定向
- `curl -o file URL`：下载并保存
- `curl -O URL`：按远端文件名保存
- `curl -X POST -H "Content-Type: application/json" -d '{"a":1}' URL`：POST JSON
- `curl ifconfig.me`：查看公网 IP
- `wget URL`：下载
- `wget -c URL`：断点续传
- `ss -tunlp`：查看监听端口
- `ss -tunlp | grep 3000`：查看 3000 端口
- `netstat -tunlp`：旧式查看端口
- `lsof -i:3000`：查看端口占用
- `nc -zv host 80`：测试端口连通
- `telnet host 80`：测试端口
- `dig domain`：DNS 查询
- `dig +short domain`：简洁 DNS 结果
- `nslookup domain`：DNS 查询
- `host domain`：DNS 查询
- `traceroute host`：路由追踪
- `mtr host`：持续路由追踪
- `tcpdump -i any port 80`：抓包
- `nmap host`：端口扫描，需授权
- `whois domain`：域名信息
- `ufw status`：防火墙状态
- `ufw allow 22`：允许 22 端口
- `ufw allow 80/tcp`：允许 80 TCP
- `ufw enable`：启用 ufw
- `ufw delete allow 80/tcp`：删除规则
- `firewall-cmd --state`：firewalld 状态
- `firewall-cmd --add-port=80/tcp --permanent`：永久开放 80
- `firewall-cmd --reload`：重载防火墙
- `iptables -L -n -v`：查看 iptables 规则
- `iptables -F`：清空规则，危险
- `nft list ruleset`：查看 nftables
- `getenforce`：查看 SELinux
- `setenforce 0`：临时关闭 SELinux

---

## 8. 磁盘、内存、性能

- `lsblk`：查看块设备
- `lsblk -f`：查看文件系统
- `blkid`：查看 UUID
- `fdisk -l`：查看磁盘分区
- `parted -l`：查看分区
- `df -hT`：查看磁盘和文件系统类型
- `du -sh *`：查看当前目录各项大小
- `ncdu`：交互式磁盘占用分析
- `mount`：查看挂载
- `mount /dev/sdb1 /mnt`：挂载
- `umount /mnt`：卸载
- `mkfs.ext4 /dev/sdb1`：格式化，危险
- `fsck /dev/sdb1`：检查文件系统，危险
- `dd if=/dev/zero of=/swapfile bs=1M count=1024`：创建 swap 文件，危险
- `smartctl -a /dev/sda`：硬盘健康
- `free -h`：内存
- `top` / `htop`：进程资源
- `vmstat 1`：虚拟内存统计
- `iostat -x 1`：磁盘 IO
- `iotop`：IO 进程
- `iftop`：网络流量
- `nload`：网卡流量
- `sar -u 1 5`：CPU 历史

---

## 9. 软件包、运行时、Docker

### Debian / Ubuntu

- `apt update`：更新软件源
- `apt upgrade -y`：升级软件
- `apt install nginx`：安装
- `apt remove nginx`：卸载
- `apt purge nginx`：卸载并删配置
- `apt search keyword`：搜索
- `apt list --installed`：已安装列表
- `dpkg -i package.deb`：安装 deb
- `dpkg -l`：列出 deb 包

### CentOS / RHEL

- `yum update`：更新
- `yum install nginx`：安装
- `yum remove nginx`：卸载
- `dnf install nginx`：新版本用法
- `rpm -ivh package.rpm`：安装 rpm
- `rpm -qa`：列出 rpm 包

### 其他

- `snap install name`：安装 snap
- `brew install name`：macOS/Linuxbrew
- `docker ps`：运行中容器
- `docker ps -a`：所有容器
- `docker images`：镜像
- `docker run -d -p 80:80 nginx`：运行容器
- `docker stop 容器`：停止
- `docker rm 容器`：删除容器
- `docker rmi 镜像`：删除镜像
- `docker logs -f 容器`：实时日志
- `docker exec -it 容器 bash`：进入容器
- `docker compose up -d`：后台启动 compose
- `docker compose down`：停止并删除 compose
- `pm2 list`：Node PM2 列表
- `pm2 restart app`：重启
- `pm2 logs app`：日志
- `nginx -t`：测试 Nginx 配置
- `nginx -s reload`：重载 Nginx
- `certbot renew --dry-run`：测试证书续期
- `htpasswd -c /etc/nginx/.htpasswd user`：创建基础认证
- `node -v`：Node 版本
- `npm -v`：npm 版本
- `pnpm -v`：pnpm 版本
- `python3 -m http.server 8000`：临时 HTTP 服务

---

## 10. 环境、历史、快捷键

- `history`：历史命令
- `history | grep ssh`：搜索历史
- `Ctrl+R`：反向搜索历史
- `!!`：执行上一条命令
- `!$`：上一条命令最后一个参数
- `alias ll='ls -lah'`：设置别名
- `unalias ll`：取消别名
- `export PATH=$PATH:/new/path`：添加 PATH
- `source ~/.bashrc`：重新加载配置
- `env`：查看环境变量
- `printenv`：查看环境变量
- `echo $PATH`：查看 PATH
- `man command`：查看手册
- `command --help`：查看帮助
- `info command`：查看 info
- `Ctrl+C`：中断当前命令
- `Ctrl+D`：退出 shell / EOF
- `Ctrl+Z`：挂起当前任务
- `Ctrl+L`：清屏
- `Ctrl+A`：行首
- `Ctrl+E`：行尾
- `Ctrl+U`：删除到行首
- `Ctrl+K`：删除到行尾
- `Ctrl+W`：删除前一个词
- `Tab`：自动补全
- `上下方向键`：历史命令

---

## 11. 常用组合示例

### 打包静态文件

```bash
tar -zcvf out.tar.gz .output/public
```

### 上传到服务器

```bash
scp out.tar.gz root@服务器ip:/www/typewords/
```

### 登录服务器

```bash
ssh root@服务器ip
```

### 解压到目标目录

```bash
tar -zxvf out.tar.gz -C /www/typewords
```

### 查看 Nginx 配置并重载

```bash
nginx -t && systemctl reload nginx
```

### 查看端口占用

```bash
ss -tunlp | grep 3000
lsof -i:3000
```

### 实时看日志

```bash
tail -f app.log
journalctl -u nginx -f
```

### 后台运行 Node

```bash
nohup npm run start > app.log 2>&1 &
```

### 找大目录

```bash
du -h --max-depth=1 | sort -h
```

### 批量替换

```bash
grep -rl "old" . | xargs sed -i 's/old/new/g'
```

### 同步目录到服务器

```bash
rsync -avz --delete .output/public/ root@服务器ip:/www/typewords/
```

### 测试端口

```bash
nc -zv 127.0.0.1 3000
```

---

## 12. 危险命令提醒

以下命令不要随便执行，尤其不要在生产服务器上试：

- `rm -rf /`
- `rm -rf *`
- `rm -rf /www/*`
- `chmod -R 777 /`
- `chown -R root:root /`
- `dd if=/dev/zero of=/dev/sda`
- `mkfs.ext4 /dev/sda1`
- `> /dev/sda`
- `crontab -r`
- `iptables -F`
- `kill -9 1`
- `find / -name "*.log" -delete`

执行删除、格式化、防火墙清空、批量替换前，先备份，先 `pwd`，先 `ls` 确认路径。
