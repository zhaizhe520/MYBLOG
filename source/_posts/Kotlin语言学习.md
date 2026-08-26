---
title: Kotlin语言学习
date: 2026-06-03 08:14:31
tags: kotlin语言学习
excerpt: Kotlin语言学习
categories: 
    - Android
---

<div style="color:pink">Kotlin 严格遵循了 驼峰命名法（CamelCase）</div>


# 变量声明：val vs var

在 Kotlin 里，你几乎找不到类似 var 在 JS 里那种变量提升的坑。它的逻辑非常干净：

val (Value)：只读变量。相当于 TS/JS 中的 const。赋值一次后就不能再变。

var (Variable)：可变变量。相当于 TS/JS 中的 let。


```

// Kotlin
val name: String = "Murasame" // 相当于 const name: string = "Murasame"
var age: Int = 18             // 相当于 let age: number = 18

// Kotlin 有极强的类型推导，类型通常可以省略：
val weapon = "Sword"          // 自动推导为 String

```

# 空安全 (Null Safety)：消灭 Cannot read property of undefined

前端最头疼的报错莫过于 TypeError: Cannot read property 'xxx' of undefined。Kotlin 极其强悍的一点就在于，它在编译期就把这个隐患给堵死了。

默认不能为 Null：在 Kotlin 里，普通的类型（比如 String）默认是绝对不允许赋 null 值的，写了编译器就直接报错。

可空类型 ?：如果你允许这个变量可能为 null，必须在类型后面加个 ?。

| 特性 | TS / JS 语法 | Kotlin 语法 |
| ---- | ------------ | ----------- |
| 可空类型声明 | let name: string \| null = null | var name: String? = null |
| 可选链 (安全调用) | name?.length | name?.length (一模一样！) |
| 空值合并 (Elvis 操作符) | const len = name ?? "Guest" | val len = name ?: "Guest" (长得像猫王) |

```
var title: String? = "Hello"
title = null // 允许的，因为有 ?

// 1. 安全调用：如果 title 是 null，直接返回 null，不会崩
println(title?.length) 

// 2. Elvis 操作符：如果是 null，就给个默认值 0
val length = title?.length ?: 0
```

# 函数与 Lambda 表达式（箭头函数）


```
// TypeScript
function add(a: number, b: number): number {
    return a + b;
}

// Kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}
```
Lambda 表达式（箭头函数）对比：
在 Kotlin 里，Lambda 是用大括号 {} 包裹的，参数写在 -> 的左边。
```
// JS / TS 的数组过滤
const nums = [1, 2, 3, 4];
const evens = nums.filter(x => x % 2 === 0);
```


```
// Kotlin 的集合过滤
val nums = listOf(1, 2, 3, 4)
val evens = nums.filter { x -> x % 2 == 0 }

// 特殊福利：如果 Lambda 只有一个参数，连参数声明都能省，直接用隐式变量 `it`
val evensSimplified = nums.filter { it % 2 == 0 }
```
# Class（类）与 Data Class（数据类）

## 传统类 (Class) 与前端对比

普通的类用来定义一个“对象模板”，里面有属性和方法。

```
Kotlin
// Kotlin 的类定义
class Player(val name: String, var hp: Int) {
    // 里面可以写方法（函数）
    fun attack() {
        println("$name 发起了攻击！")
    }
}

fun main() {
    // 实例化一个对象（注意：Kotlin 不需要像 JS 那样写 new 关键字！）
    val hero = Player("Murasame", 100)
    hero.attack() // 输出: Murasame 发起了攻击！
}
```

## Data Class（数据类）

data  class User1

data class 自带的三大前端狂喜功能：

自动格式化输出：普通的类如果你打印它，会输出一串看不懂的内存地址（如 Player@61bbe9ba）。但 data class 会自动转成漂亮的字符串，长得像 JSON 一样！

自带解构赋值：和 JS/TS 的结构一模一样。

自带复制/浅拷贝 (copy)：改动某个属性生成新对象极其方便（类似于 JS 的解构复写 {...user, name: 'NewName'}）。

```
data class User(val id: Int, val name: String, val role: String)

fun main() {
    val user1 = User(1, "Murasame", "Admin")
    
    // 1. 打印它，自动变成漂亮可读的格式
    println(user1) // 输出: User(id=1, name=Murasame, role=Admin)
    
    // 2. 解构赋值 (和 TS 的 const { name, role } = user1 很像)
    val (id, name, role) = user1
    println("名字是: $name")
    
    // 3. 浅拷贝：复制 user1，但把名字改掉 (类似于 JS 的 {...user1, name: "Rena"})
    val user2 = user1.copy(name = "Rena")
    println(user2) // 输出: User(id=1, name=Rena, role=Admin)
}
```

# 条件分支。

## 基础when用法

```
fun main() {
    val role = "Admin"

    // Kotlin 的 when 语法
    when (role) {
        "Admin" -> println("你是超级管理员")
        "User" -> println("你是普通用户")
        "Guest" -> println("你是游客")
        else -> println("未知角色") // 相当于 JS 的 default
    }
}
```


## 进阶版：when 居然是一个“表达式”！

在 JS/TS 里，switch 是一个语句，你不能把它直接赋值给一个变量。
但在 Kotlin 里，if 和 when 都是表达式，意思是它们可以直接返回一个值！这在写 Vue 的计算属性（Computed）或者状态切换时，爽翻天：

```
Kotlin
fun main() {
    val statusCode = 200

    // 直接把 when 的结果赋值给 val 变量！
    val httpMessage = when (statusCode) {
        200, 201 -> "成功"          // 支持多个条件用逗号隔开
        in 400..499 -> "客户端错误"  // 支持区间判断（400 到 499 之间）
        in 500..599 -> "服务器错误"
        else -> "未知状态码"
    }

    println("请求状态：$httpMessage") // 输出: 请求状态：成功
}
```


# if / else 也是表达式

```
fun main() {
    val a = 10
    val b = 20

    // 代替前端的 const max = a > b ? a : b;
    val max = if (a > b) a else b
    
    println("最大值是: $max") // 输出: 20
}
```