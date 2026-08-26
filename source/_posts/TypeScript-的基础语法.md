---
title: TypeScript 的基础语法
date: 2026-06-30 09:29:40
tags: TypeScript 的基础语法
excerpt: TypeScript 的基础语法
categories: 
    - TypeScript
---

TS = JS + 类型安全


# 基础类型声明（给变量加保镖）
```
// 1. 基础类型
let age: number = 25;
let name: string = "Miku";
let isIdol: boolean = true;

// 2. 数组类型（两种写法的效果完全一样）
let scores: number[] = [90, 85, 95]; 
let skills: Array<string> = ["Vue", "Node", "TS"];

// 3. 任意类型 (any) —— 相当于关掉 TS 的检查，退回原生 JS 
// 工业界一般不建议用，但在刚从 JS 转过来的过渡期可以用它救急
let anything: any = "我爱变啥变啥";
anything = 123;
```

# 函数的类型约束（管好输入和输出）
```
// 定义一个名为 User 的接口
interface User {
    id: number;
    username: string;
    email: string;
    age?: number; // 💡 注意这里的问号：表示这个属性是可选的（可有可无）
}

// 实际使用：这个对象必须严格遵守 User 的结构
const myUser: User = {
    id: 1001,
    username: "田中",
    email: "tanaka@example.com"
    // age 没写也不会报错，因为它是可选的
};

// ❌ 报错示例：
const badUser: User = {
    id: 1002,
    username: "佐藤"
    // ❌ 报错：漏掉了必填的 email 属性！
};
```
# 接口（Interface）—— 全栈开发的绝对灵魂 🌟
```
// 定义一个名为 User 的接口
interface User {
    id: number;
    username: string;
    email: string;
    age?: number; // 💡 注意这里的问号：表示这个属性是可选的（可有可无）
}

// 实际使用：这个对象必须严格遵守 User 的结构
const myUser: User = {
    id: 1001,
    username: "田中",
    email: "tanaka@example.com"
    // age 没写也不会报错，因为它是可选的
};

// ❌ 报错示例：
const badUser: User = {
    id: 1002,
    username: "佐藤"
    // ❌ 报错：漏掉了必填的 email 属性！
};
```