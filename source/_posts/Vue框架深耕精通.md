---
title: Vue3框架深耕精通
date: 2026-09-03 15:07:38
tags: Vue框架深耕精通
excerpt: Vue框架深耕精通
sticky: 80
categories: 
    - Vue框架
---
官方文档: [https://cn.vuejs.org]

<details>
<summary>API</summary>

# 响应式 API（Reactivity）

| API | 作用 | 要点 |
| ---- | ---- | ---- |
| ref | 基本类型响应式 | 也可以放对象；访问需要 `.value`；对象内部会自动转为reactive |
| reactive | 对象/数组响应式 | 仅支持引用类型；直接访问属性，不需要`.value`；不能解构，解构丢失响应 |
| computed | 计算属性 | 依赖自动缓存；依赖变化才重新计算；可传get/set实现读写 |
| watch | 侦听器 | 显式指定监听目标；默认惰性（初始不执行）；可获取新旧值 |
| watchEffect | 自动追踪依赖侦听 | 不用手动写监听源；立即执行；自动收集内部用到的响应式依赖 |
| readonly | 只读代理 | 返回原数据只读代理；修改直接警告；浅层只读，内部对象仍可改 |
| shallowRef | 浅层ref | 只对 `.value` 本身做响应；value如果是对象，对象内部不响应 |
| shallowReactive | 浅层响应式 | 只有第一层属性响应，深层嵌套对象不会被代理 |
| toRef | 把对象单个属性转为ref | 保持和原对象关联；解构单独属性保留响应性 |
| toRefs | 对象全部属性批量转ref | 用于reactive对象解构，保证解构后属性依然响应；只转第一层 |
| effectScope | 副作用作用域 | 批量收集内部watch、watchEffect等副作用；可一次性stop清除 |
| markRaw | 标记对象永不响应 | 对象永远不会被proxy代理；ref/reactive碰到它直接原样保留 |

# 生命周期钩子

| 组合式API | 选项式API | 说明 |
| ---- | ---- | ---- |
| onBeforeMount | beforeMount | 挂载之前，DOM还没创建 |
| onMounted | mounted | 挂载完成，DOM已生成，可操作DOM |
| onBeforeUpdate | beforeUpdate | 数据更新前，DOM还没重新渲染 |
| onUpdated | updated | 数据更新后，DOM更新完成 |
| onBeforeUnmount | beforeUnmount | 组件销毁前，实例还可用 |
| onUnmounted | unmounted | 组件销毁完成，清理定时器/事件监听 |
| onActivated | activated | keep-alive缓存组件**激活时**触发 |
| onDeactivated | deactivated | keep-alive缓存组件**失活时**触发 |
| onErrorCaptured | errorCaptured | 捕获后代组件抛出的错误 |

# 组件 / 渲染 API

| API | 作用 | 要点 |
| ---- | ---- | ---- |
| h | 创建虚拟VNode | 渲染函数用；参数：标签,属性,子节点；template编译后底层就是h |
| defineComponent | 定义组件 | 主要给TS提供类型推导；运行时几乎无效果，选项式/setup都能用 |
| defineAsyncComponent | 异步组件 | 组件懒加载；加载完成才渲染，可配置加载/错误占位 |
| defineCustomElement | 把Vue组件转为Web Component | 原生自定义元素，脱离Vue框架也能在页面使用 |
| resolveComponent | 按字符串名称解析组件 | 渲染函数/h里，根据组件名拿到组件定义，需注册过 |
| nextTick | 等待DOM更新完成 | DOM更新是异步；在回调里拿到最新真实DOM |
| mergeProps | 合并多个props对象 | 自动处理class/style合并，覆盖优先级从左到右 |
| useSlots | 获取插槽 | script setup中使用，拿到slots对象，slots.default()渲染默认插槽 |
| useAttrs | 获取attrs | script setup中，获取透传属性（非props、非事件） |

# 依赖注入

| API | 作用 | 要点 |
| ---- | ---- | ---- |
| provide | 向上层组件提供依赖 | 父/祖先组件注入数据、方法；key-value，跨多层传递 |
| inject | 后代组件注入获取依赖 | 从祖先组件读取provide提供的值；可设置默认值 |

# 内置组件

| 内置组件 | 作用 | 要点 |
| ---- | ---- | ---- |
| KeepAlive | 缓存组件实例 | 组件被销毁时不释放实例；切走触发onDeactivated，切回触发onActivated；不执行onMounted重复挂载 |
| Transition | 单个元素/组件过渡动画 | 进入、离开添加class；只渲染单个根节点 |
| TransitionGroup | 列表多节点过渡 | 用于v-for列表；每一项都能做过渡；必须指定key |
| Teleport | 把DOM渲染到页面别的位置 | 脱离当前组件DOM层级，常见弹框放到body；逻辑还属于当前组件实例 |
| Suspense | 异步内容加载占位 | 等待异步组件/async setup；加载中显示fallback，加载完成替换；捕获顶层异步 |
| component | 动态组件 | :is 切换组件；配合KeepAlive缓存切换的组件实例 |

# 编译器宏（`<script setup>` 专用）

| 宏 | 作用 | 要点 |
| ---- | ---- | ---- |
| defineProps | 定义组件props | script setup专属宏；声明接收的属性；返回props对象 |
| defineEmits | 定义事件 | 声明可触发的事件；返回emit函数，用于触发事件通知父组件 |
| defineExpose | 暴露属性/方法给父组件 | 默认script setup组件实例对外是关闭的，需要手动暴露，父通过$ref访问 |
| defineSlots | 插槽类型定义 | TS环境，用于约束插槽入参类型，运行时代码无效果 |
| defineOptions | 直接写组件选项 | script setup里直接写name、inheritAttrs等，不用额外`<script>`块 |
| defineModel | 双向绑定props | Vue3.4+新增；简化v-model，内部封装props + emit |
| withDefaults | props默认值 | 配合defineProps，给props设置默认值，TS类型保留 |

# 应用实例 API

| API | 作用 | 要点 |
| ---- | ---- | ---- |
| createApp | 创建Vue应用实例 | 接收根组件；一个页面可多个独立app实例 |
| app.use | 安装插件 | 插件需要暴露install函数；如vue-router、pinia |
| app.component | 注册全局组件 | 全局任意组件可直接使用，无需局部导入 |
| app.directive | 注册全局自定义指令 | 全局生效，用于DOM底层操作 |
| app.provide | 全局注入依赖 | 整个应用内所有后代组件都可inject获取 |
| app.mount | 挂载应用 | 传入选择器/DOM节点；执行后渲染页面，返回根组件实例 |

</details>

<details>
<summary>指令</summary>

| 指令 | 作用 | 要点 |
| ---- | ---- | ---- |
| v-if | 条件渲染 | 条件为false时**销毁/移除DOM**；切换开销大，初始为false更合适；不能和v-for同写一个标签 |
| v-else / v-else-if | 配合v-if | 必须紧跟v-if或v-else-if，中间不能插别的元素 |
| v-show | 条件显示 | 只是CSS切换`display:none`，DOM一直存在；切换频繁优先使用；不支持`<template>` |
| v-for | 列表渲染 | 循环生成节点；必须绑定key；优先级高于v-if，尽量不要同元素共用 |
| v-bind（简写:） | 绑定HTML属性 | 动态赋值；可绑定class/style；`v-bind="$attrs"`批量透传属性 |
| v-on（简写@） | 绑定事件 | 绑定原生/自定义事件；支持修饰符`.stop` `.prevent` `.once` |
| v-model | 双向绑定 | 语法糖；本质是 :modelValue + @update:modelValue；组件支持多v-model（Vue3） |
| v-slot（简写#） | 插槽 | 定义/接收插槽；默认插槽、具名插槽、作用域插槽 |
| v-html | 渲染HTML | 解析html字符串渲染；**存在XSS安全风险**，不要渲染用户输入 |
| v-text | 渲染文本 | 替换元素内部全部文本，等同于插值`{{}}`，无html解析 |
| v-once | 只渲染一次 | 首次渲染后缓存，后续数据更新不再重渲染 |
| v-memo | 缓存子树 | Vue3；传入依赖数组，依赖不变，跳过该子树diff；优化复杂长列表 |
| v-pre | 跳过编译 | 内部内容不做Vue模板编译，原样展示`{{}}` |
| v-cloak | 隐藏未编译模板 | 配合css`[v-cloak]{display:none}`，解决页面闪烁（vue2遗留，Vite基本不用） |

自定义指令

导出一个对象，对象上挂载`mounted / updated / unmounted` 这些函数

Vue内部会在对应生命周期自动调用这些函数，传入 `el、binding、vnode、prevVNode`参数。`

vue3帮我操作dom 想浏览器的api一样

</details>

<details>
<summary>修饰符</summary>

| 指令 | 修饰符 | 作用 | 要点 |
| ---- | ---- | ---- | ---- |
| v-on / @ | .stop | 阻止事件冒泡（event.stopPropagation） | 事件不会向父元素传递 |
| v-on / @ | .prevent | 阻止浏览器默认行为（event.preventDefault） | 阻止a跳转、form提交 |
| v-on / @ | .once | 事件只执行1次，之后不再触发 | 一次性点击 |
| v-model | .lazy | 失焦后才同步数据 | 默认input输入实时更新，lazy改成change事件 |
| v-model | .number | 自动把输入内容转为数字类型 | 无法转数字则保留原始字符串 |
| v-model | .trim | 自动去除首尾空格 | 输入前后空白自动剪掉 |
| v-bind | .prop | 把值作为DOM的property绑定，不是attribute | 比如绑定input.checked，而不是HTML属性 |

</details>

基于函数范式底层思想哲学  ``

框架类型: 渐进式 声明式 响应式 组件化 基于`MVVM`思想的实现

```txt
WeakMap (targetMap)
 └── key: target (被代理的原始对象 obj)
      └── value: Map (depsMap)
           └── key: key (对象的属性名, 如 'name')
                └── value: Set (dep)
                     └── 包含该属性的所有 side effect 函数 (ReactiveEffect)
```

`依赖track()收集   触发更新trigger()`;

<details>
<summary>JS与Vue框架碰撞产生的不良</summary>

JS源生语法与vue框架API 产生的冲突

</details>

<details>
<summary>MVVM:Model‑View‑ViewModel思想</summary>

`Model(JS数据) ↔ ViewModel(Vue响应式) ↔ View(页面模板)·`

</details>

命令式（原生 JS/jQuery）：手动选 DOM、改 DOM 内容。

框架本质: 底层封装了个`对象的读写操作，自动做【依赖收集】和【派发更新】`

<details>
<summary>Vue2和vue3封装劫持区别</summary>

**Vue2：`Object.defineProperty`**
遍历对象**每一个属性**，给属性添加 getter/setter 劫持。
缺点：只能监听初始化就存在的属性；新增、删除属性、数组下标修改无法监听，需要`$set`。
内部模型：`Dep` 存放 watcher。

```js
基本语法:Object.defineProperty(obj, propertyName, descriptor)

//伪劫持代码块

Object.defineProperty(obd,"propertyName"){
    //接收
    get(){}
 
    //派发
    set(){}
}

// 原始数据
const data = { name: "张三" }
let _value = data.name
// 劫持这个name属性
Object.defineProperty(data, 'name', {
  get() {
    // ✅ 读取数据的时候触发get → track 依赖收集
    console.log('读取name，收集依赖')
    return this._value
  },
  set(newVal) {
    // ✅ 修改数据的时候触发set → trigger 派发更新
    console.log('修改name，通知视图更新')
    this._value = newVal
  }
})

data.name // 触发get
data.name = "李四" // 触发set

```

已存在的属性`{ name: "张三" }`

不存在的属性`{age: 18}` 要触发set就要上`$set`

```js

// 模拟$set给新增属性加上劫持
function my$set(obj,key,value){
  Object.defineProperty(obj,key,{
    get(){
      console.log(`读取${key}`)
      return value
    },
    set(newVal){
      console.log(`修改${key}`,newVal)
      value = newVal
    }
  })
  obj[key] = value
}

my$set(data,'age',22)
data.age = 30 // ✅触发set，打印日志

```

**Vue3：`Proxy + Reflect`**
直接代理**整个对象**，拦截对象所有读写、增删操作。

懒代理：只有访问嵌套对象才做响应式，不用初始化递归全部遍历，性能更好。

内部模型：`targetMap` 存储对象 → 属性 → effect 映射。

```js
const raw = { name: "张三" }

// 代理整个对象
const proxy = new Proxy(raw, {
  get(target, key) {
    // 读取属性，收集依赖 track
    console.log(`读取属性:${key}`)
    // Reflect 保证this指向正确
    return Reflect.get(target, key)
  },
  set(target, key, newVal) {
    // 修改属性，派发更新 trigger
    console.log(`设置属性${key}=${newVal}`)
    return Reflect.set(target, key, newVal)
  }
})

proxy.name       // 触发proxy get
proxy.name="李四"// 触发proxy set
proxy.age=20     // ✅ 新增属性也能劫持！Vue2做不到

```

proxy  只能代理「对象/数组」，不能直接劫持 number/string/boolean 这类基本类型

ref 就是把基础类型包成一个小对象，靠  .value  的 getter/setter 做拦截；如果ref传对象，内部自动调用reactive套Proxy 。

```js
function ref(value) {
  // 如果传入对象，内部自动用 reactive(Proxy)包装
  if (typeof value === 'object') {
    value = reactive(value)
  }
  return {
    get value() {
      track() // 收集依赖（记录哪个组件在用这个ref）
      return value
    },
    set value(newVal) {
      value = newVal
      trigger() // 触发更新，页面重新渲染
    }
  }
}
```

<details>
<summary>3层映射表存依赖 track() trigger() </summary>

```txt
WeakMap (targetMap)
 └── key: target (被代理的原始对象 obj)
      └── value: Map (depsMap)
           └── key: key (对象的属性名, 如 'name')
                └── value: Set (dep)
                     └── 包含该属性的所有 side effect 函数 (ReactiveEffect)
```

/*试着写一track依赖搜集与trigger分发订阅

//开辟一条新的弱引用内存 能被GC回收

```ts
// 1. 创建全局顶层 WeakMap 容器（targetMap）
// Key: 被代理的原始对象 (object)
// Value: 该对象对应的属性依赖表 (Map)
const targetMap = new WeakMap<object, Map<string, Set<Function>>>();

// 2. 依赖收集函数：track（当读取对象属性时调用）
function track(target: object, key: string, effectFn: Function) {
  // ① 从 WeakMap 中查找当前对象的依赖表
  let depsMap = targetMap.get(target);
  if (!depsMap) {
    depsMap = new Map<string, Set<Function>>();
    targetMap.set(target, depsMap); // 建立第一层：WeakMap -> Map
  }

  // ② 从 depsMap 中查找当前属性的 Set 集合
  let dep = depsMap.get(key);
  if (!dep) {
    dep = new Set<Function>();
    depsMap.set(key, dep); // 建立第二层：Map -> Set
  }

  // ③ 将副作用函数存入 Set 中（第三层：Set 存 effectFn）
  dep.add(effectFn);
  console.log(`[依赖收集] 成功收集属性 "${key}" 的依赖函数！`);
}

// 3. 依赖触发函数：trigger（当修改对象属性时调用）
function trigger(target: object, key: string) {
  const depsMap = targetMap.get(target);
  if (!depsMap) return;

  const dep = depsMap.get(key);
  if (dep) {
    console.log(`[触发更新] 属性 "${key}" 发生变化，开始执行依赖函数...`);
    dep.forEach(effectFn => effectFn());
  }
}

// ==================== 🛠️ 测试运行 ====================

// 假设我们有一个用户对象
let user: { name: string; age: number } | null = { name: "张三", age: 22 };

// 定义一个更新 UI 的副作用函数
const renderUI = () => {
  console.log(`UI 渲染更新：用户姓名是 ${user?.name}`);
};

// 模拟读取 user.name，触发依赖收集
track(user, "name", renderUI);

// 模拟修改 user.name，触发派发更新
trigger(user, "name"); // 输出：UI 渲染更新：用户姓名是 张三

// 💡 重点：内存回收演示
// 当业务逻辑结束，将 user 变量置为 null
user = null; 
// 此时，因为 targetMap 是 WeakMap，GC（垃圾回收器）在下一次清理时，
// 会自动将原来的 user 对象以及它在 targetMap 中对应的 Map/Set 数据全部清空，完美防范内存泄漏！

```

```ts
// 1. 原始对象 user = {name:"张三",age:22}
// 2. renderUI 副作用函数，读取user.name
// 3. track(user, "name", renderUI)
//    - targetMap没有user，新建depsMap存入targetMap
//    - depsMap没有key "name"，新建Set(dep)
//    - 把renderUI放进Set
// 4. trigger(user, "name")
//    - 根据user找到depsMap，再找到name对应的dep集合
//    - 遍历dep执行renderUI，打印UI更新
// 5. user = null
//    外部不再持有user对象引用。WeakMap不阻止GC，user对象连同targetMap里面对应的整条依赖记录，会被垃圾回收清理。
```

</details>

activeeffet() 全局变量

3层表

get() ---->track()

set() ----->trigger()

</details>

<details>
<summary>computed 完整知识架构、底层模型、哲学</summary>

惰性求值

computed 是响应式系统里的「自动缓存的纯派生公式」

**computed 计算属性**：**派生状态（derived state）**。

它**只读、自动缓存、基于响应式依赖做纯计算；不会修改任何外部变量，不改变外部世界**。 它**没有副作用**。

区分「源状态（source state）」和「派生状态 (derived state)」

源状态：真相源头，ref/reactive 保存。

派生状态：不需要独立存储，完全可以由源状态推导得出。不要单独存一份！ 消除冗余状态

vue2 {a+b} --->每次都会触发从渲染

能推导出来的数据，就不要单独保存。让 computed 自动推导，保证永远和源状态一致。

|概念|computed|watch|
|----|----|----|
|定位|派生状态，求值、产生值|依赖变化，执行动作，处理副作用|
|范式|声明式|命令式|
|执行时机|惰性，读取才执行|依赖变更，主动触发回调|
|返回|返回一个响应式包装的值|无返回，一般用来做 side effect|
|能不能放副作用|getter 禁止；setter 可以（慎用）|专门用来承载副作用|
|哲学|根据源状态自动算出新状态，消除冗余状态|状态变化之后，协调外部世界|

>无论多少次访问  都会立即返回先前的计算结果，而不用重复执行 getter 函数


</details>

<details>
<summary>处理副作用的载体</summary>
set()

</details>

<details>
<summary>template标签</summary>

```txt
模板字符串
  → parse（词法+语法分析）
  → Template AST（节点：Element、Text、Interpolation、Directive...）
  → transform（转换，处理 v-if/v-for/插值等）
  → codegen（生成代码）
  → render 函数
```

自己写的 `词法分析`

```txt
模板 → Template AST → render 函数
                          ↓ 执行 render
                       VNode 树（虚拟 DOM）
                          ↓ patch （diff算法）
                      真实 DOM
```

编译时优化 + 最小化 DOM 操作 + 渲染引擎原生机制

语法树 :AST

render()函数

vnode js嵌套对象

patch 阶段 操作源生dom

</details>

<details>
<summary>开屏快</summary>

编译时优化 + 最小化 DOM 操作 + 渲染引擎原生机制

</details>

<details>
<summary>Vue 3 的 diff：patchKeyedChildren</summary>

```
更新触发
  → 组件重新 render → 新 VNode 树
  → patch(oldVNode, newVNode, container)
      ├─ 类型/key 不同 → unmount 旧的 + mount 新的
      └─ 相同 → 进入细分处理
          ├─ 元素：patchProp 更新属性/事件/class/style  ← 调原生 DOM API
          └─ 子节点：patchChildren
              ├─ 文本 vs 数组 等简单情况
              └─ 数组 vs 数组 → patchKeyedChildren（diff）
                  ├─ 头尾同步
                  ├─ 新增 / 删除
                  └─ 乱序 → key 映射 + LIS → 最少 move
                      └─ move/insert → hostInsert → 原生 insertBefore
```

同步头部

同步尾部

只有新增

只有删除

乱序：最长递增子序列（LIS）

</details>


