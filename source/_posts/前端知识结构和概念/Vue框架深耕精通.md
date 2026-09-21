---
title: Vue3框架深耕精通
date: 2026-09-03 15:07:38
tags: Vue框架深耕精通
excerpt: Vue框架深耕精通
sticky: 80
categories: 
    - Vue框架
---
官方文档: [https://cn.vuejs.org/]

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



</details>

Vue 自定义指令，就是导出一个对象，对象上挂载`mounted / updated / unmounted` 这些函数，Vue内部会在对应生命周期自动调用这些函数，传入 `el、binding、vnode、prevVNode`参数。`
