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

框架类型: 渐进式 声明式 响应式 组件化 基于`MVVM`思想的实现

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

</details>

Vue 自定义指令，就是导出一个对象，对象上挂载`mounted / updated / unmounted` 这些函数，Vue内部会在对应生命周期自动调用这些函数，传入 `el、binding、vnode、prevVNode`参数。`
