---
title: Spring Boot
date: 2026-06-21 21:02:19
tags: Spring Boot写后端
excerpt: Spring Boot写后端
categories: 
    - 后端
---

<div>前端请求 → Controller → Service → Mapper → MySQL</div>


简化 Spring 开发的脚手架框架

自动配置：不用写 XML，导入依赖自动装配功能

内置服务器：自带 Tomcat，不用单独安装、配置 Tomcat

起步依赖 Starter：需要什么功能直接引入一行依赖，自动配套所有相关 jar 包

用 Java 做 Web 接口、后台服务，现在行业标准就是 SpringBoot。


# 提供 @RestController、@GetMapping、@PostMapping

快速写 GET/POST 接口，接收前端 JSON 参数，返回 JSON 数据。

```
@RestController
public class SearchController {
    @GetMapping("/api/search")
    public String search(String keyword){
        return "你搜索的关键词：" + keyword;
    }
}
```

# 数据库操作（Spring Data JPA / MyBatis）

操作 MySQL、Oracle 等数据库，不用手写大量 JDBC 代码：

MyBatis：中小企业最常用，灵活写 SQL

MyBatis-Plus：简化 CRUD，单表增删改查几乎不用写 SQL


# 配置文件

只有一个配置文件 application.yml / application.properties

统一管理：端口、数据库地址、Redis、文件路径等所有配置，不用到处改代码。

```
application.yml 示例
server:
  port: 8080 # 服务运行端口
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test_db
    username: root
    password: 123456
```

# 内置工具生态

开箱即用：日志、全局异常处理、参数校验、跨域、文件上传、Redis 缓存、定时任务。

# SpringBoot 项目标准分层（后端通用规范）


所有 SpringBoot 项目都固定 5 层，分工清晰：

`Controller 控制层`

接收前端请求，暴露 /api/xxx 接口，只负责接收参数、返回结果，不写业务逻辑。

`Service 业务层`

写核心业务逻辑（判断、计算、多表操作），Controller 调用 Service。

`Mapper/DAO 数据访问层`

专门操作数据库，查询、新增、修改、删除数据。

`Entity/Model 实体类`

和数据库表一一对应，存储数据（用户、商品、文章等实体）。

`VO/DTO 传输对象`

专门用来接收前端参数、返回给前端的数据，过滤敏感字段。

# 学习 SpringBoot 前置要求（基础 Java）

`ava 基础：类、对象、集合 List/Map、注解、异常、IO`

`MySQL 基础：建表、增删改查、简单关联查询`

`简单 HTTP 知识：GET/POST、JSON、请求参数`

