---
title: TypeScript全量开发
date: 2026-06-30 09:29:40
tags: TypeScript全量开发
excerpt: TypeScript全量开发
sticky: 50
categories: 
    - TypeScript
---

[https://www.typescriptlang.org/docs/handbook/intro.html]

# TypeScript 开发

需要熟练掌握类型定义、泛型、接口（Interfaces）以及 Vue 全家桶中的 TS 类型推导与扩展。

# TypeScript类比JS补充的功能

类型声明与检查（如 name: string）。

编译期访问修饰符（public / protected / private）。

构造函数参数简写（如 constructor(public name: string)）。

抽象类与接口实现（如 implements Interface）。

<details>
<summary>class|interface|type</summary>

# class

# interface

最小契约 全传完

对象、类、函数 的“结构”或“形状” shap

```ts
interface name{
  name : string 
}

const my: name{
  name: "翟哲"
}
```

讲究继承 extends im

# type

给属性值赋予限定？

type  name = "zhaizhe"| "nihao" | "zyhe"

还能联合？

& 交叉合并

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

# readonly 修饰符

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

protected construct() {}

```ts
class A{
  protected construct(public a:string) {}
  //不能在类A 里面new 实例
  say(){
    console.log(a)
  }
}

//const B = new A (); X 外部不能new A

class B extends A{
  construct(){
    super(999)
  }
}


const b = new B ()

b.say()
```

```ts
//基础抽象模版
abstract class user{
  construct (public a:string){}
  abstract say(){};
}
```

</details>

<details>
<summary>T 泛型</summary>

`Record<K, V>` 是 TS 内置的泛型工具类型

# 泛型函数

```ts
function name<T> (age: T): T{
  console.log("1")
  return age
} 

const my = name<string>("1")
```

# 泛型接口

```ts
interface cat<T>{
  value: T;
}

const my: cat<string> ={
  value: "mao mao"
}
```

# 泛型类

```TS
class <T>{

}
```

</details>

# any  unknown

any 不做类型检查

unknown 不知道啥类型  收窄才能用

```ts
const my: Record<string, unknown>{
  name: "zz"
}


const name = my.name

name.toUpperCase();  XXX 没收窄

if( typeof name === "string"){
  name.toUpperCase();
}

if(typeof === ""){} 收窄
```

收窄类型 类型守卫

# void null undefined 0

viod 空的 ts静态函数标记 不关心返回值

null 空的主动赋值

undefined 必须返回 undefined

# 静态编译器 .tsc

ts ----> ast ---->
