---
title: Java学习
date: 2026-06-26 21:49:02
tags: Java学习
excerpt: Java学习
categories:
    - Java
---
🚀 第一梯队：必须立刻掌握的“核心硬通货”

# 类（Class）与 对象（Object）

在 Spring Boot 里的角色：你从数据库查出来一条用户数据，Java 不能直接把它当成一坨 JSON 丢着。

你必须先在 Java 里定义一个 User 类（包含 id、username、password 属性）

然后把查出来的数据变成一个 User 对象。

这个类在后端有个专业术语叫 POJO / Entity（实体类）。

# 集合（List / Map）

这是后端处理数据的绝对核心，相当于 JS 里的数组和对象。

List：就是数组（比如`List<User> list`），专门用来装列表数据。比如你从数据库查出“用户列表”，Java 就会用一个 List 把他们装起来。

Map：就是 键值对（Key-Value）（比如 `Map<String, Object>`）。

它非常像 JS 里的普通 Object。当你想临时拼一个自定义的 JSON 返回给 Vue 前端，但又懒得建一个新的类时，直接用 Map 塞数据最方便。

# 注解（Annotation）—— Spring Boot 的灵魂

怎么理解：就是代码里那些带 @ 符号的东西（比如 @RestController、@GetMapping、@CrossOrigin）。

在 Spring Boot 里的角色：Java 的注解就像是“贴标签”。

你写一个普通的类，只要在上面贴一个 @RestController 标签，Spring Boot 就会自动把它变成一个可以接收网络请求的 API 控制器。

学 Spring Boot，本质上很大一部分就是在学怎么用这些注解。

🛠️ 第二梯队：遇到了再查的“功能挂件”




# 异常处理（Exception）

怎么理解：类似于前端的 try...catch。

在 Spring Boot 里的角色：后端太容易报错了（比如数据库断开了、前端传错参数了）。

Java 有一套非常严密的异常机制。在 Spring Boot 里，我们会写一个“`全局异常捕获器`”，不管后端哪里报错，都统一拦截下来，包装成一个 { code: 500, msg: "服务器冒烟了" } 的标准 JSON 丢给 Vue，不让前端页面崩溃。

# IO（输入输出流）

怎么理解：Java 用来读写文件、处理上传下载的工具。

在 Spring Boot 里的角色：如果你现在只是写增删改查的接口，暂时用不到 IO。只有当你要做“用户上传头像”、“后端生成 Excel 导出报表”功能时，才会用到 Java 的 IO 流。先不用死磕它。


