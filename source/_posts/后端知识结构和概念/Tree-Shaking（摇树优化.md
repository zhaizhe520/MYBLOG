---
title: Tree Shaking（摇树优化)
date: 2026-06-02 21:44:40
tags: Tree Shaking（摇树优化)
excerpt: Tree Shaking（摇树优化)
categories: 
    - 优化
---
# 🪵 为什么需要 Tree Shaking？（背景）

在现代软件开发中（无论是 Vue / React 还是 Android），我们极度依赖 模块化。

比如你为了用一个“计算日期相差几天”的功能，引入了一个叫 utils.js 或者一个第三方的 DateLib 依赖库。这个库里可能包含了 100 个函数（算农历、算星座、算时区……），但你其实只用了其中的 1 个函数。

如果不做处理，打包工具会把整套 100 个函数全部打包进最终的 App 里。这就导致用户下载的文件体积臃肿。Tree Shaking 就是为了消灭这些“占着茅坑不拉屎”的僵尸代码（Dead Code）。

# ⚙️ Tree Shaking 是怎么工作的？

无论是前端的 Vite / Rollup / Webpack，还是安卓里的 R8 / ProGuard，它们搞 Tree Shaking 的核心思想可以分为三步：

## 静态分析（Static Analysis）

这是 Tree Shaking 的前置条件。编译器在不运行代码的情况下，只靠阅读你的源码，来梳理代码之间的引用关系。

前端（JavaScript）： 必须依赖 ES Modules (ESM)，也就是你天天写的 import 和 export。因为 ESM 的依赖关系是静态的、固定的。

(反例：老旧的 CommonJS require() 可以写在 if 判断里动态加载，编译器在打包时根本猜不到你到底要不要加载，所以 CommonJS 很难做 Tree Shaking。)

## 构建“依赖图”（Dependency Graph）

编译器会从你的入口文件（比如 Vue 的 main.js，或者安卓的 MainActivity.kt）开始，像侦探顺藤摸瓜一样去数：

main.js 引用了 A组件。

A组件 内部调用了 utils.js 里的 formatDate 函数。

所有被“点名”调用过的代码，都会被贴上一个标签：【Alive（活着的）】。

摇树（Shaking）

当整棵依赖树梳理完毕后，编译器会把目光投向那些从未被贴上标签的孤立函数或组件。

```
Plaintext
【你的项目依赖树】
   ├── main.js (入口)
   │    └── import { format } from './utils.js' (被使用 -> 保留)
   │
   └── utils.js 内部未被引用的代码
        ├── export function calculateMoonPhase() {} ──❌ (无人引用 -> 摇掉！)
        └── export function getZodiacSign() {}     ──❌ (无人引用 -> 摇掉！)
在最终生成生产环境代码（.js 文件或 .apk 包）时，编译器直接闭着眼把这些无人引用的代码行全部抹除。
```

⚠️ 前端开发中的 Tree Shaking “大坑”：副作用（Side Effects）

在实际写代码（特别是写 Vue 组件或发布 npm 包）时，Tree Shaking 经常会遇到一个让它不敢下手的克星——副作用（Side Effects）。

什么叫副作用？就是某行代码即使没有被别的人显式调用，但它一旦在文件中执行，就会对外界产生影响。

举个例子 🌰
假设你写了一个 analytics.js 文件，里面没有任何 export：
```
JavaScript
// analytics.js
window.globalTracker = "Yasina"; // 修改了全局变量！
console.log("偷偷在后台发送了一个埋点日志");
然后你在 main.js 里只是简单地：
```
JavaScript
import './analytics.js';
这时候，打包工具（如 Vite）会非常纠结：“这个文件里没有任何函数被别人调用，按理说我可以把它整棵树摇掉。但是！它居然悄悄修改了全局的 window 对象，还打印了日志！如果我把它摇掉了，程序运行结果就会发生改变！”

为了安全起见，打包工具会放弃对它的 Tree Shaking，只能把它完完整整地打包进去。

💡 怎么解决？
作为前端开发者，如果你在写一个公共组件库，你可以在 package.json 里显式地告诉打包工具：
```
JSON
{
  "name": "my-vue-components",
  "sideEffects": false
}
```
这等于向打包工具打包票：“放心摇！我这个库里的代码非常干净，没有任何偷摸修改全局变量的副作用，谁没被 import 谁就是没用，你丫放心大胆地把它们剪掉！”

# 🤖 移动端（Android R8）的 Tree Shaking 有何不同？
你在 Android Studio 里正在经历的，是比前端更严苛的字节码级别的 Tree Shaking。

不仅摇代码，还摇资源： 安卓的 R8 编译器不仅能把没用到的 Kotlin/Java 类和方法剪掉（Shrinking），还能通过 shrinkResources 把你放进 res 文件夹里、但代码里从未调用过的图片（PNG/JPG）、布局（XML）文件全部自动删掉或者替换成 1 字节的空文件！

反射（Reflection）是它的天敌： 在安卓里，如果有些代码是通过字符串反射动态调用的（比如通过反射去寻找叫 "Murasame" 的类），R8 在静态分析时也是看不出来的。为了防止 R8 把这些类误当成“垃圾”摇掉，安卓工程师必须写一个 proguard-rules.pro 文件，写上 -keep class ...，手动告诉编译器：“大侠手下留情，这几根树枝看着是枯的，其实后面有用，千万别摇！”

# 🎯 总结
Tree Shaking 是现代软件工程“既要开发爽，又要体积小”的完美解决方案。
它允许你像今天这样，在开发时漫山遍野地下载几十兆的依赖、尽情享受庞大生态带来的便利，却在最终交付给用户时，通过极其硬核的数学图论算法，把体积压缩到极致。