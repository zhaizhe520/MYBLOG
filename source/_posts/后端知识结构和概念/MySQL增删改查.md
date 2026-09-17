---
title: MySQL增删改查（CRUD）
date: 2026-04-25 16:54:17
tags: MySQL
excerpt: 数据库MySQL
categories:
    - MySQL
---
# mysql -u root -p

密码:xxx

SHOW DATABASES;
USE XXX;
SHOW TABLES;


# 新建数据库


CREATE DATABASE xxxx CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
create database xxxx character set(字符集) utf8mb4 collate (校对规则)utf8mb4_unicode_ci(不区分大小写);

# 新建数据表


create table 名字(
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    release_date DATE,
    xx


);

#  基本操作


show databases; 查库

use xxx;  用表


show tables;  查表


desc xxxx;  描述表


select 字段 from 表  limit 10; 查数据

# 增

<div style="color:pink">避免数据串线不能增自增的id</div>

## 增数据

单增： insert into 表 (字段) values ('数据');

多增： insert into 表（字段, 字段，字段,字段）values ('数据'),('数据'),('数据'),('数据');
## 增字段

<div style="color:pink">给 xxx 表增加一个叫 xxx 的字段，类型是 TEXT（可以存好几万字）</div>

ALTER TABLE xxxx ADD COLUMN 名字 类型 null;可以为空

alter table 表 add  column  名字 类型 null; 可以为空 


# 删

<div style="color:pink">避免数据串线不能删自增的id</div>

单删： DELETE FROM 表 WHERE 字段 = '数据';

多删:  DELETE FROM 表 WHERE 字段 IN ('数据1', '数据2');

<div style="color:pink">清空数据,自增重新开始</div>没有外键的情况下

TRUNCATE TABLE 表;

truncate table 表;

<div style="color:red">清空数据,自增继续</div>

DELETE FROM 表;

删完如果有自增就让从1开始就是了
ALTER TABLE 表 AUTO_INCREMENT = 1;
alter table 表 auto_increment = 1;
删字段

ALTER TABLE 表 DROP COLUMN 字段;

删除整个表

DROP TABLE IF EXISTS users;


# 换 补 

UPDATE 表 SET 字段 = '新数据' WHERE 字段 = '旧数据';

调头写法，都是从表到字段 select 字段 from 表 limit xx;(不是)读取顺序是的

<div style="color:pink">补</div>

UPDATE 表 

SET  空的字段 = 'xxxx' 

WHERE 字段（一般是id） = 'xxxx';


# 二个关系型数据表


一对多关系（One-to-Many）—— 最常见 “主从表结构”（或叫 父子表结构）。

多对多关系（Many-to-Many）多对多，建中间表



建立外键约束，关联到 companies 表的 id


脏数据 

孤儿数据

僵尸数据

## 一对多关系

外键（Foreign Key）

fk_子表_父表

CREATE TABLE works (
    id INT AUTO_INCREMENT PRIMARY KEY,   -- 1. 给每个作品一个独立的编号，它自己会自动往上加（1,2,3...）
    title VARCHAR(255) NOT NULL,        -- 2. 存作品的名字（比如"千恋＊万花"），不能为空
    company_id INT NOT NULL,            -- 3. 核心：用来存“公司暗号”的格子
    
    下面这三行是告诉数据库一个“铁律”：
    CONSTRAINT fk_work_company 
    constraint fk_work_company 
    FOREIGN KEY (company_id) REFERENCES companies(id)
    foreing key  (company_id) peferences companies(id)
    ON DELETE CASCADE
    on delete cascade
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
FOREIGN KEY (company_id) REFERENCES companies(id)

这是外键约束。

意思是：“数据库你帮我盯着点！以后往作品表里填 company_id 的时候，这个数字必须在公司表的 id 里能找到。

要是输入一个不存在的数字（比如填个 999），直接报错拒绝，不让存！” 防止你粗心录错数据。

ON DELETE CASCADE（级连删除）
on delete cascade


意思是：“如果有一天我把‘柚子社’（id: 1）从公司表删除了，你（数据库）懂事点，自动把作品表里所有 company_id 是 1 的作品（千恋万花、魔女的夜宴）全部一起删掉。” 省得你手动去一个个删。

# 数据库里的三大 Key 状态（对照看就懂了）

|缩写|全称|大白话含义|举例|
| ---- | ---- | ---- | ---- |
|PRI|Primary Key 主键|数值唯一不重复，且不能为空，一张表仅能设置一个|作品表的唯一编号id|
|UNI|Unique Key 唯一索引|数值不能重复，允许为空值|用户表的手机号、邮箱|
|MUL|Multiple 普通索引/外键|数值可重复出现，无唯一性限制|作品表的所属公司编号company_id|


# 表自增重置（重置自增计数器。）
-- 1. 移除现有的自增主键属性
ALTER TABLE 表 MODIFY id INT;
ALTER TABLE 表 DROP PRIMARY KEY;

-- 2. 清空 id 字段的值（全部变成 0）
UPDATE 表 SET id = 0;

-- 3. 重新添加自增主键，MariaDB 会自动从 1 开始重新按顺序填充所有 id
ALTER TABLE 表 MODIFY id INT AUTO_INCREMENT PRIMARY KEY;

##  清空表（如果你想彻底重新开始）
TRUNCATE TABLE 表;
truncate table  表;

## 删除最大的重新开始排序
ALTER TABLE 表名 AUTO_INCREMENT = 新的起始数字;
下一次你执行不带 id 的 INSERT 时，数据库就会乖乖地从 新的起始数字 开始往下排。


##  数据串线

不能是手动去补id？破坏自增？

##  空缺”本身就是一种非常重要的历史记录。
空缺”帮你保留了数据的“作废标记
在关系型数据库的世界里，残缺的 ID 序列，才是最完美、最健康的艺术品。

|方式|核心命令|适用场景|危险程度|
| ---- | ---- | ---- | ---- |
|1. 强制重排 (全变)|ALTER TABLE ... DROP PRIMARY KEY|只有几条测试数据，强迫症发作，非要让所有老数据的 ID 变成连续的 1, 2, 3。|🔴 极高（会把子表的关联全部破坏掉）|
|2. 摧毁重建 (全清)|TRUNCATE TABLE 表名;|彻底不要这表里的数据了，想一键清空，并且让新数据从 1 开始。|🔴 极高（数据全没了，有外键时会报错）|
|3. 修改计数器 (只改未来)|ALTER TABLE ... AUTO_INCREMENT = X|最正规的做法。老数据不动，只规定下一条新数据从几开始。|🟢 安全（不会影响任何已有数据）|

# 联合查询
```
SELECT 
    父表别名.父表字段, 
    父表别名.父表字段, 
    子表别名.子表字段, 
    子表别名.子表字段
FROM 
    父表名 父表别名
INNER JOIN (关键词：交集)
    子表名 子表别名 
0N 
    父表别名.父表主键 = 子表别名.子表外键
WHERE 
    别名.id='xxx';

```

# 返回JOIN数据方便axios查

在 MariaDB 和 MySQL 中，如果你想把两张有关联的表做联合查询（JOIN），同时又想让输出的结果直接变成 JSON 格式（方便前端 API 调用或者存入非关系型数据库），数据库提供了几个非常强大的内置 JSON 函数。


## 方案一：把每一行数据转成一个 JSON 对象（最常用）
如果你希望查询结果的每一行都是一个独立的 JSON 对象，包含游戏信息和它所属的公司名称，可以使用 JSON_OBJECT。

# json包裹输入

```
SELECT JSON_OBJECT(
    'id', w.id,
    'game_title', w.title,
    'company', c.company_name
) AS json_data
FROM works w
JOIN companies c ON w.company_id = c.id;

```
JSON_OBJECT(...) 负责把一行的字段拼成 {}。

JSON_ARRAYAGG(...) 负责把多行 {} 聚合成一个 []。


`JSON_OBJECT('键1', 值1, '键2', 值2, '键3', 值3, ...)`

奇数参数（第 1, 3, 5... 个）：必须是字符串，代表将来 JSON 里的 key（键名）。

偶数参数（第 2, 4, 6... 个）：可以是列名、常量、或者表达式，代表 JSON 里的 value（值）。

## JSON_OBJECT 的内部，还可以再塞一个 JSON_OBJECT！这就是为什么它能完美处理父子表的原因。

# 日期格式

YYYY-MM-DD

# 技术



# “软删除”（Soft Delete）或状态控制

##  添加 is_deleted字段（删除标记）状态列（最推荐）

修改表结构（添加字段）：

ALTER TABLE 表 ADD COLUMN is_deleted TINYINT(1) DEFAULT 0;

0 表示有效（默认）。

1 表示已删除/无效。


让xxx行数据“无效”：

UPDATE 表 SET is_deleted = 1 WHERE id = xx;

业务查询时过滤：

以后你在前台或者 API 查询数据时，永远加上一个条件：WHERE is_deleted = 0。


## 方案二：添加 status字段（状态）状态列

修改表结构（添加状态字段）：

ALTER TABLE 表 ADD COLUMN status VARCHAR(20) DEFAULT 'active';

将第 xx 行改为下架/无效状态：

UPDATE 表 SET status = 'disabled' WHERE id = xxx;


查询时只查有效状态：

SELECT * FROM your_table_name WHERE status = 'active';

# 临时绝招（如果你现在无法修改表结构）前端过滤 时间戳写法？

```
UPDATE 表
SET 
    字段 = '[已失效/已隐藏]', 
    delete_time IS NULL
WHERE id = xxx;
```

然后在代码里判断：如果 title 包含 [已失效]，就不在界面上渲染这一行。

# 必须包含 Person 表中的每一个人，即使某个人的主键 personId 在 Address 表中不存在

左外连接（LEFT JOIN）

select 
p.firstName,
p.lastName,
a.city,
a.state
FROM person p LEFT JOIN address a on p.personId =a.personId;