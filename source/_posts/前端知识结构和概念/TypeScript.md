---
title: TypeScript语法
date: 2026-06-30 09:29:40
tags: TypeScript语法
excerpt: TypeScript语法
sticky: 50
categories: 
    - TypeScript
---

# TypeScript 开发

需要熟练掌握类型定义、泛型、接口（Interfaces）以及 Vue 全家桶中的 TS 类型推导与扩展。


# TypeScript类比JS补充的功能：

类型声明与检查（如 name: string）。

编译期访问修饰符（public / protected / private）。

构造函数参数简写（如 constructor(public name: string)）。

抽象类与接口实现（如 implements Interface）。

<details>
<summary>类和接口</summary>




</details>



<details>
<summary>修饰符</summary>

# public  修饰符


# protected 修饰符

```ts

class AbstractController {
  // 限制构造函数仅允许子类调用
  protected constructor(protected moduleName: string) {}
}

// const ctrl = new AbstractController("User"); // ❌ 报错：构造函数受保护，无法直接实例化 不能new

class UserController extends AbstractController {
  constructor() {
    super("UserModule"); // ✅ 子类可以通过 super() 正常调用
  }
}

// JS 原生 #field 语法：在运行时强制私有（由 JavaScript 引擎提供保障），任何外部手段都无法突破限制 object.freeze()

class user {
  #name: string 
  constructor(){
    this.#name = "tom"
  }
}

const a = new user()

// a.name = xxx no
```


# private 修饰符


#  readonly 修饰符

```ts
//readonly  修饰的属性只能在声明时或构造函数中赋值，之后不能再修改

class user{
  readonly into={name:"tom"}
}

const a = new user()

a.into={} //no

a.into.name = "jom" //yes

//静态类型校验 编译成js之后没有readonly  



```

# 新旧Class写法

```ts
//在构造函数参数前加修饰符（如 public readonly），TS 会自动完成"声明属性 + 赋值"两步，等价于省略赋值的过程 在construct()+ 修饰符


// 写法 A（简写） 构造函数+语法糖 没有修饰符不会生成具体实例
class A {
  constructor(public readonly id: number) {}
}

// 写法 B（等价展开） 
class B {
  public readonly id: number;
  constructor(id: number) {
    this.id = id;
  }
}
```
</details>

<details>


<summary>抽象类与接口实现</summary>




</details>

