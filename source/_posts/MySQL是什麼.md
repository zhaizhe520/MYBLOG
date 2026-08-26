---
title: MySQL的来历
date: 2026-04-10 11:05:59
tags: MySQL与MariaDB的爱恨情仇
categories:
  - MySQL
---
# MySQL属于关系型数据库（RDBMS）
用来存数据：账号、文章、订单、评论、配置…… 所有网站要长期保存的东西
和 PHP + Nginx 是经典三件套：LNMP / WAMP

# 不是軟件
不是软件界面，是一个服务，你用命令或工具连接它，然后用 SQL 语言操作：
SQL 就是用来跟数据库（比如 MySQL）说话的语言，专门用来查、增、改、删数据。
最简单理解
MySQL 是数据库 = 仓库
-- 查数据
SELECT * FROM users;

-- 新增数据
INSERT INTO users(name, age) VALUES('小明', 20);

-- 修改数据
UPDATE users SET age=21 WHERE name='小明';

-- 删除数据
DELETE FROM users WHERE name='小明';

📜 为什么不用 MySQL，而是 MariaDB？
其实，这两款数据库的亲爹是同一个人（迈克尔·维德纽斯，Michael Widenius）。

MySQL 的诞生： 老爷子最早开发了开源的 MySQL，并用他大女儿的名字 My 命名。MySQL 因为免费、好用，迅速成为了全世界最火的数据库。

资本的介入： 后来，MySQL 几经转手，最终被科技巨头 Oracle（甲骨文） 收购了。甲骨文这家公司在开源界名声有点……一言难尽（商业化收钱手段非常狠）。大家开始担心：万一哪天甲骨文把 MySQL 改成全面收费，或者不开源了怎么办？

老爹的愤怒与反击： 数据库老爹一看，自己养大的娃要变资本的摇钱树了，一气之下从甲骨文离职，直接把 MySQL 的源码复制了一份出来（因为当时是开源的），重新做了一个数据库。这次，他用他小女儿的名字 Maria 命名，这就是 MariaDB。

所以，MariaDB 是 MySQL 的亲妹妹，也是它的“完美替代版”。

🔄 它们俩一样吗？（开发者的真实体验）
在实际写代码、敲命令时，它们有 95% 以上是完全共通的。

命令完全通用： 你在网上看的任何 MySQL 教程，里面的命令（比如 SELECT * FROM...，INSERT INTO...）在 MariaDB 里一字不改，直接敲，完全能用。

连驱动都通用： 你的后端（比如 Node.js、Python、PHP）连接数据库时，甚至可以直接用 mysql2 这种针对 MySQL 的插件去连 MariaDB，它们俩连端口号默认都是一模一样的 3306。

MariaDB 的优势： 因为没有大公司的商业包袱，MariaDB 迭代速度极快，在很多时候性能比原版 MySQL 还要好一点，而且它承诺永远开源免费。这也是为什么现在的很多 Linux 服务器系统（比如 CentOS、Debian）默认装的都是 MariaDB。