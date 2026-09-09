---
title: TypeScript语法
date: 2026-06-30 09:29:40
tags: TypeScript语法
excerpt: TypeScript语法
sticky: 50
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

<details>
<summary>TS 指令类型声明</summary>

# ObjectDirective（对象指令类型）

是什么：它定义了一个自定义指令对象应该长什么样。

作用：告诉 TS：“我定义的这个变量是一个 Vue 自定义指令，里面包含 mounted、unmounted、updated 等钩子函数”。

好处：如果你把钩子函数名字拼错了（比如写成了 onMounted 或 mount），TS 会立刻弹红提示你写错了。

# DirectiveBinding（指令绑定参数类型）

是什么：它定义了在生命周期钩子（如 mounted(el, binding)）里，第二个参数 binding 内部包含哪些属性。

作用：告诉 TS：binding 里面有 value（传进来的值）、oldValue、arg（参数）、modifiers（修饰符）等属性。

好处：当你敲出 binding. 时，编辑器（如 VS Code）会自动弹出 value、oldValue 的自动补全和提示，不需要再去死记硬背。


| 参数 | 类型 | 说明 |
| ---- | ---- | ---- |
| el | HTMLElement (或自定义扩展) | 真实的原生 DOM 节点<br>绑定原生事件（addEventListener）、修改 style 样式。 |
| binding | DirectiveBinding&lt;T&gt; | 指令包含的属性对象<br>获取 binding.value（传进来的值）、binding.arg（如 v‑pet:foo 中的 foo）。 |
| vnode | VNode | Vue 的虚拟 DOM 节点<br>获取当前节点对应的 Vue 组件实例（vnode.component）、上下文信息等高级操作。 |
| prevVNode | VNode \| null | 旧的虚拟 DOM 节点<br>主要在 updated 钩子中做新旧 VNode 比较（Diffing）。 |

# 类型补丁


|  | 类型来源 | 示例 | 作用 |
| ---- | ---- | ---- | ---- |
| 1 | TS / JS 原生内置类型 | HTMLElement / string / number | 描述标准 DOM 或原生数据类型 |
| 2 | 框架提供的类型 | ObjectDirective / DirectiveBinding / VNode | 描述 Vue 内部的特定结构 |
| 3 | 自己用 interface 定义的类型 | PetNovelElement | 根据业务需求灵活补全或自定义数据结构 |


</details>

状态共享（单例与工厂模式）