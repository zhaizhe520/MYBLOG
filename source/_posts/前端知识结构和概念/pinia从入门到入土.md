---
title: Pinia从入门到入土
date: 2026-04-27 20:00:58
tags:
excerpt: pinia从入门到入土
categories:
    - Vue框架
---
# Pinia 全面详解：Vue3 状态管理首选方案（从入门到实战，避坑指南全收录）

在 Vue 生态中，状态管理一直是核心需求之一。从早期的 Vuex 到如今的 Pinia，Vue 官方推荐的状态管理方案不断迭代，只为给开发者更简洁、高效、易维护的开发体验。Pinia 作为 Vue 官方正式推荐的新一代状态管理库，不仅完全替代了 Vuex，更在 API 设计、类型支持、性能优化上实现了全方位升级，尤其适配 Vue3 组合式 API，成为当下 Vue 项目（尤其是中大型项目）的首选状态管理方案。

本文将从 Pinia 的核心定位、核心概念、快速上手、进阶用法、实战技巧、踩坑指南、与 Vuex 对比等多个维度，进行全面、详细的总结，既适合新手入门学习，也适合开发者作为实战参考，助力大家轻松掌握 Pinia 并灵活运用到项目中。

## 一、Pinia 核心定位与优势

Pinia 是由 Vue 官方团队开发维护的状态管理库，诞生的核心目的是解决 Vuex 在 Vue3 时代的适配问题，同时简化状态管理的使用流程，提升开发效率和代码可维护性。其核心优势相较于 Vuex 有了质的提升，具体如下：

### 1\. 极简 API，降低上手成本

Pinia 最直观的优势就是简化了 API 设计，去掉了 Vuex 中繁琐的 mutations 配置——无论是同步逻辑还是异步逻辑，都可以统一放在 actions 中处理，无需区分同步 mutation 和异步 action，大大减少了代码冗余，降低了新手的学习成本。同时，Pinia 无需嵌套 modules，每个 store 独立存在，结构更清晰，维护更方便。

### 2\. 原生 TypeScript 支持，类型安全拉满

Vue3 原生支持 TypeScript，而 Pinia 作为 Vue3 生态的核心组件，天生具备完善的类型推导能力。无需额外配置，就能实现状态、getters、actions 的自动类型补全和类型校验，避免了 Vuex 中需要手动定义类型、配置复杂类型映射的问题，极大提升了 TypeScript 项目的开发体验，减少类型错误。

### 3\. 轻量高效，性能优异

Pinia 的体积非常小巧，仅约 1KB（gzip 压缩后），远小于 Vuex 的 10KB 左右，对项目打包体积影响微乎其微。同时，Pinia 内部做了大量性能优化，比如状态的响应式实现基于 Vue3 的 reactivity 系统，避免了不必要的性能损耗，在大型项目中表现更稳定、更高效。

### 4\. 完善的 Devtools 集成

Pinia 完美集成了 Vue Devtools，支持状态追踪、时间旅行、热更新等功能，开发者可以清晰地看到每一次状态的变化、触发的 actions，方便调试和问题定位，大大提升了开发效率。

### 5\. 兼容性强，灵活适配

Pinia 不仅完美适配 Vue3 的组合式 API（Composition API），也支持 Vue2 的选项式 API（Options API），可以无缝迁移 Vue2 项目中的 Vuex 代码；同时，它既可以用于小型项目，也能满足中大型项目的复杂状态管理需求，灵活性极高。

## 二、Pinia 核心概念详解（3大核心\+1个辅助）

Pinia 的核心概念非常简洁，只有 3 个核心（Store、State、Actions）和 1 个辅助概念（Getters），每个概念的职责清晰，易于理解和使用。

### 1\. Store（仓库）：状态管理的容器

Store 是 Pinia 中最基础的概念，相当于一个“容器”，用于存储某一个业务模块的状态、计算属性和方法。每个 Store 都有一个全局唯一的 id（必填），用于区分不同的 Store，避免冲突。

核心特点：

- 一个 Store 对应一个业务模块（如用户模块 userStore、购物车模块 cartStore、订单模块 orderStore），实现状态的模块化拆分，避免全局状态臃肿。

- Store 是惰性创建的，只有在首次使用（如组件中调用 useXXXStore\(\)）时才会被创建，提升项目初始化性能。

- 通过 defineStore\(\) 函数创建，支持两种写法：选项式（Options API 风格）和组合式（Composition API 风格），推荐 Vue3 项目使用组合式写法。

基础示例（选项式）：

```typescript
import { defineStore } from 'pinia'

// 第一个参数是 Store 的唯一 id，第二个参数是配置对象
export const useCounterStore = defineStore('counter', {
  state: () => ({}), // 状态
  getters: {}, // 计算属性
  actions: {} // 方法
})
```

### 2\. State：唯一数据源，存储核心状态

State 是 Store 中的核心，用于存储该模块的所有状态数据，相当于组件中的 data\(\)，是响应式的——当 State 中的数据发生变化时，依赖该数据的组件会自动更新。

核心注意点：

- State 必须用箭头函数返回一个初始对象，这样可以避免多个 Store 实例之间的状态污染（确保每个实例都有独立的状态副本）。

- State 中的数据可以是任意类型（基本类型、对象、数组等），均支持响应式。

- 可以直接修改 State 中的数据，无需像 Vuex 那样通过 mutation，简化操作。

基础示例：

```typescript
export const useCounterStore = defineStore('counter', {
  // 箭头函数返回初始状态
  state: () => ({
    count: 0, // 基本类型
    userInfo: { name: '', age: 0 }, // 对象类型
    list: [1, 2, 3] // 数组类型
  })
})
```

### 3\. Getters：基于 State 的派生计算属性

Getters 相当于组件中的 computed，用于基于 State 中的数据派生新的状态，具有缓存特性——只有当依赖的 State 数据发生变化时，Getters 才会重新计算，否则直接返回缓存的结果，提升性能。

核心用法：

- Getters 可以接收 state 作为第一个参数，用于访问 Store 中的状态。

- Getters 可以通过 this 访问自身的其他 Getters（注意：选项式写法中 this 指向 Store 实例，组合式写法需直接调用）。

- Getters 支持传参（通过返回一个函数实现），适用于需要根据动态参数派生状态的场景。

示例（包含三种用法）：

```typescript
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 10,
    price: 9,
    list: [1, 2, 3, 4, 5]
  }),
  getters: {
    // 1. 基础用法：基于 state 派生
    doubleCount: (state) => state.count * 2,
    
    // 2. 访问自身其他 getters
    totalPrice: function(state) {
      // this 指向 Store 实例，可访问 doubleCount
      return this.doubleCount * state.price
    },
    
    // 3. 传参用法：返回一个函数接收参数
    filterList: (state) => (min: number) => {
      return state.list.filter(item => item > min)
    }
  }
})
```

组件中使用 Getters：

```vue
<script setup>
import { useCounterStore } from '@/stores/counter'
const counter = useCounterStore()

// 基础用法
console.log(counter.doubleCount) // 20

// 传参用法
console.log(counter.filterList(3)) // [4, 5]
</script>
```

### 4\. Actions：处理同步/异步逻辑，修改 State

Actions 相当于组件中的 methods，用于处理该模块的所有逻辑（同步和异步），并直接修改 State 中的数据（无需通过 mutation）。Actions 是 Pinia 中处理业务逻辑的核心，也是实现跨 Store 通信的关键。

核心用法：

- Actions 可以是同步函数，也可以是异步函数（async/await），支持处理接口请求等异步操作。

- Actions 中可以通过 this 访问 Store 中的 state、getters 和其他 actions（选项式写法），组合式写法需直接调用对应变量和函数。

- Actions 可以接收参数，用于动态修改状态。

- 异步 Actions 可以返回 Promise，组件中可以通过 await 等待其执行完成，便于处理后续逻辑。

示例（同步\+异步）：

```typescript
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
    userInfo: null
  }),
  actions: {
    // 1. 同步 action
    increment(n: number) {
      // 直接修改 state
      this.count += n
    },
    
    // 2. 异步 action（模拟接口请求）
    async fetchUserInfo(userId: number) {
      try {
        const res = await fetch(`/api/user/${userId}`)
        const data = await res.json()
        // 异步请求完成后，修改 state
        this.userInfo = data
        // 返回数据，供组件使用
        return data
      } catch (error) {
        console.error('获取用户信息失败：', error)
        throw error // 抛出错误，供组件捕获
      }
    }
  }
})
```

组件中调用 Actions：

```vue
<script setup>
import { useCounterStore } from '@/stores/counter'
const counter = useCounterStore()

// 调用同步 action
counter.increment(5)

// 调用异步 action（await 等待执行完成）
const getUserInfo = async () => {
  try {
    const user = await counter.fetchUserInfo(123)
    console.log('用户信息：', user)
  } catch (error) {
    // 处理错误
  }
}
getUserInfo()
&lt;/script&gt;
```

## 三、Pinia 快速上手（Vue3 \+ TypeScript 实战）

下面通过一个完整的实战案例，从安装、全局注册、定义 Store 到组件中使用，一步步带你上手 Pinia，确保新手也能快速落地。

### 1\. 安装 Pinia

在 Vue3 项目中，通过 npm 或 yarn 安装 Pinia：

```bash
// npm
npm install pinia

// yarn
yarn add pinia
```

### 2\. 全局注册 Pinia

在项目入口文件（main\.ts）中，创建 Pinia 实例并挂载到 Vue 应用上：

```typescript
import { createApp } from 'vue'
import { createPinia } from 'pinia' // 引入 createPinia
import App from './App.vue'

// 创建 Pinia 实例
const pinia = createPinia()
// 创建 Vue 应用并挂载 Pinia
const app = createApp(App)
app.use(pinia) // 注册 Pinia
app.mount('#app')
```

### 3\. 定义 Store（两种写法）

Pinia 支持两种 Store 写法：选项式（适合 Vue2 迁移或习惯 Options API 的开发者）和组合式（适合 Vue3 项目，推荐），下面分别给出示例。

#### （1）选项式写法（Options API 风格）

在 src/stores 目录下创建 counter\.ts（按业务模块划分目录，规范管理）：

```typescript
import { defineStore } from 'pinia'

// 定义并导出 Store
export const useCounterStore = defineStore('counter', {
  // 状态：初始数据
  state: () => ({
    count: 0,
    name: 'Pinia 示例',
    isShow: true
  }),
  // 计算属性：基于 state 派生
  getters: {
    doubleCount: (state) => state.count * 2,
    // 访问自身 getters
    doubleCountPlusOne: function() {
      return this.doubleCount + 1
    }
  },
  // 方法：处理逻辑
  actions: {
    // 同步方法
    increment() {
      this.count++
    },
    // 带参数的同步方法
    setCount(value: number) {
      this.count = value
    },
    // 异步方法
    async fetchCount() {
      // 模拟接口请求
      const res = await new Promise(resolve => {
        setTimeout(() => resolve(100), 1000)
      })
      this.setCount(res as number)
      return res
    }
  }
})
```

#### （2）组合式写法（Composition API 风格，推荐）

组合式写法与 Vue3 的 \&lt;script setup\&gt; 风格完全统一，使用 ref、computed 定义状态和计算属性，普通函数定义方法，更简洁、更灵活：

```typescript
import { defineStore } from 'pinia'
import { ref, computed } from 'vue' // 引入 Vue3 的响应式 API

export const useCounterStore = defineStore('counter', () => {
  // 状态：用 ref 定义，对应选项式的 state
  const count = ref(0)
  const name = ref('Pinia 组合式示例')
  const isShow = ref(true)

  // 计算属性：用 computed 定义，对应选项式的 getters
  const doubleCount = computed(() => count.value * 2)
  const doubleCountPlusOne = computed(() => doubleCount.value + 1)

  // 方法：普通函数，对应选项式的 actions
  const increment = () => {
    count.value++
  }
  const setCount = (value: number) => {
    count.value = value
  }
  const fetchCount = async () => {
    const res = await new Promise(resolve => {
      setTimeout(() => resolve(100), 1000)
    })
    setCount(res as number)
    return res
  }

  // 导出需要在组件中使用的状态、计算属性和方法
  return {
    count,
    name,
    isShow,
    doubleCount,
    doubleCountPlusOne,
    increment,
    setCount,
    fetchCount
  }
})
```

### 4\. 组件中使用 Store

在 Vue 组件中，通过 useXXXStore\(\) 函数获取 Store 实例，然后访问状态、调用方法即可。需要注意的是，直接解构 Store 中的状态会丢失响应式，必须使用 storeToRefs 进行响应式解构。

```vue
<template>
  <div class="counter-container">
    <h2>{{ name }}</h2>
    <p>当前计数：{{ count }}</p>
    <p>计数的2倍：{{ doubleCount }}</p>
    <p>计数的2倍+1：{{ doubleCountPlusOne }}</p>
    <button @click="increment">+1</button>
    <button @click="setCount(0)">重置</button>
    <button @click="handleFetchCount">异步获取计数</button>
  </div>
</template>

<script setup>
// 1. 引入 Store
import { useCounterStore } from '@/stores/counter'
// 2. 引入 storeToRefs（用于响应式解构）
import { storeToRefs } from 'pinia'

// 3. 获取 Store 实例
const counter = useCounterStore()

// 4. 响应式解构：状态和 getters 用 storeToRefs
const { count, name, doubleCount, doubleCountPlusOne } = storeToRefs(counter)

// 5. 直接解构 actions（无需 storeToRefs，不影响响应式）
const { increment, setCount, fetchCount } = counter

// 6. 调用异步 action
const handleFetchCount = async () => {
  await fetchCount()
  alert('异步获取计数完成！')
}
</script>
```

## 四、Pinia 进阶用法（实战必备）

掌握基础用法后，下面这些进阶技巧能帮你更好地应对复杂项目场景，提升开发效率和代码质量。

### 1\. State 修改的三种方式（批量修改\+函数式修改）

Pinia 支持直接修改 State、批量修改和函数式修改三种方式，可根据场景灵活选择：

```typescript
// 1. 直接修改（适合单个状态修改）
counter.count = 10

// 2. 批量修改（适合多个状态同时修改，用 $patch 方法）
counter.$patch({
  count: 20,
  name: '批量修改示例',
  isShow: false
})

// 3. 函数式 $patch（适合复杂修改，如数组操作、条件修改）
counter.$patch((state) => {
  state.count += 5
  state.list.push(6, 7, 8) // 数组操作
  if (state.count > 30) {
    state.isShow = false
  }
})
```

注意：函数式 $patch 的优势是可以避免多次修改状态导致的组件多次更新，提升性能，尤其适合复杂的状态修改场景。

### 2\. State 重置（$reset 方法）

场景：退出登录、页面重置等需要将状态恢复到初始值的场景，可使用 $reset 方法快速重置 State：

```typescript
// 组件中调用
counter.$reset()

// 组合式写法中，$reset 方法需要手动实现（因为组合式没有默认的 state 选项）
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const name = ref('Pinia 示例')

  // 手动实现 $reset 方法
  const $reset = () => {
    count.value = 0
    name.value = 'Pinia 示例'
  }

  return { count, name, $reset }
})
```

### 3\. 跨 Store 通信（不同模块状态交互）

中大型项目中，多个 Store 之间难免需要交互（如用户登录后，更新购物车的用户信息），Pinia 实现跨 Store 通信非常简单：在一个 Store 中引入另一个 Store 的 useXXXStore\(\) 函数，实例化后即可访问其状态和方法。

示例（userStore 中调用 cartStore）：

```typescript
// src/stores/user.ts
import { defineStore } from 'pinia'
// 引入 cartStore
import { useCartStore } from './cart'

export const useUserStore = defineStore('user', () => {
  const username = ref('')

  // 登录方法：登录后更新购物车的用户信息
  const login = async (userData: { name: string }) => {
    // 模拟登录接口
    await new Promise(resolve => setTimeout(resolve, 500))
    username.value = userData.name

    // 跨 Store 调用：实例化 cartStore
    const cartStore = useCartStore()
    // 调用 cartStore 的方法，更新购物车用户信息
    cartStore.setUserName(userData.name)
  }

  return { username, login }
})

// src/stores/cart.ts
import { defineStore } from 'pinia'
import { ref } from 'vue'

export const useCartStore = defineStore('cart', () => {
  const userName = ref('')
  const cartList = ref([])

  const setUserName = (name: string) => {
    userName.value = name
  }

  return { userName, cartList, setUserName }
})
```

### 4\. 状态持久化（pinia\-plugin\-persistedstate 插件）

默认情况下，Pinia 的状态会在页面刷新后丢失（因为状态存储在内存中）。对于需要持久化的状态（如用户登录信息、购物车数据），可以使用 pinia\-plugin\-persistedstate 插件，将状态持久化到 localStorage 或 sessionStorage 中。

步骤：

1. 安装插件：

```bash
npm install pinia-plugin-persistedstate
```

1. 全局注册插件（main\.ts）：

```typescript
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate' // 引入插件
import App from './App.vue'

const pinia = createPinia()
pinia.use(piniaPluginPersistedstate) // 注册插件
const app = createApp(App)
app.use(pinia)
app.mount('#app')
```

1. 在需要持久化的 Store 中开启 persist 配置：

```typescript
// 选项式写法
export const useUserStore = defineStore('user', {
  state: () => ({ username: '', token: '' }),
  persist: true // 开启持久化，默认存储到 localStorage
})

// 组合式写法
export const useCartStore = defineStore('cart', {
  state: () => ({ cartList: [] }),
  persist: true
})
```

高级配置（自定义存储方式、存储字段）：

```typescript
export const useUserStore = defineStore('user', {
  state: () => ({ username: '', token: '', age: 0 }),
  persist: {
    key: 'user_info', // 自定义存储的 key，默认是 Store 的 id
    storage: sessionStorage, // 自定义存储方式，可选 localStorage（默认）、sessionStorage
    paths: ['username', 'token'] // 只持久化指定字段，不写则持久化所有状态
  }
})
```

### 5\. Store 订阅（监听状态变化）

场景：需要监听 State 中某个或所有状态的变化，执行自定义逻辑（如日志记录、埋点），可使用 Store 的 $subscribe 方法。

```typescript
const counter = useCounterStore()

// 订阅所有状态变化
const unsubscribe = counter.$subscribe((mutation, state) => {
  // mutation：变化信息（如变化的字段、旧值、新值）
  // state：变化后的完整状态
  console.log('状态变化：', mutation, state)
  // 示例：记录日志
  console.log(`count 从 ${mutation.oldValue.count} 变为 ${mutation.newValue.count}`)
})

// 取消订阅（组件卸载时调用，避免内存泄漏）
onUnmounted(() => {
  unsubscribe()
})

// 订阅指定状态变化（通过 filter 过滤）
counter.$subscribe((mutation) => {
  if (mutation.events[0].key === 'count') {
    // 只监听 count 的变化
    console.log('count 变化了')
  }
})
```

## 五、Pinia 常见踩坑指南（新手必看）

在使用 Pinia 的过程中，新手很容易遇到一些问题，下面整理了最常见的 5 个踩坑点，帮你避坑。

### 1\. 直接解构 Store 丢失响应式

❌ 错误做法：直接解构 Store 中的状态和 getters，导致数据失去响应式，修改后组件不更新。

```typescript
// 错误：直接解构，丢失响应式
const { count, doubleCount } = counter
```

✅ 正确做法：使用 storeToRefs 解构状态和 getters，actions 可以直接解构。

```typescript
import { storeToRefs } from 'pinia'

// 正确：响应式解构
const { count, doubleCount } = storeToRefs(counter)
// actions 直接解构
const { increment } = counter
```

### 2\. Store 的 id 不唯一

❌ 错误做法：多个 Store 使用相同的 id，导致状态冲突、数据错乱。

```typescript
// 错误：两个 Store 的 id 都是 counter
export const useCounterStore1 = defineStore('counter', { ... })
export const useCounterStore2 = defineStore('counter', { ... })
```

✅ 正确做法：每个 Store 的 id 全局唯一，建议按业务模块命名（如 user、cart、counter）。

```typescript
export const useUserStore = defineStore('user', { ... })
export const useCartStore = defineStore('cart', { ... })
export const useCounterStore = defineStore('counter', { ... })
```

### 3\. 组合式写法中忘记导出状态/方法

❌ 错误做法：组合式写法中，定义了状态或方法，但没有在 return 中导出，导致组件中无法访问。

```typescript
// 错误：count 没有导出
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const increment = () => count.value++
  // 忘记导出 count
  return { increment }
})
```

✅ 正确做法：所有需要在组件中使用的状态、getters、方法，都必须在 return 中导出。

### 4\. 异步 Actions 没有使用 await，导致逻辑顺序错误

❌ 错误做法：调用异步 Actions 时，没有使用 await，导致后续逻辑在异步操作完成前执行。

```typescript
// 错误：没有 await，console.log 会先执行
counter.fetchCount()
console.log(counter.count) // 输出 0（异步操作还未完成）
```

✅ 正确做法：使用 await 等待异步 Actions 执行完成，再执行后续逻辑。

```typescript
// 正确：await 等待异步操作完成
await counter.fetchCount()
console.log(counter.count) // 输出 100（异步操作完成后的值）
```

### 5\. 滥用全局状态，将组件私有数据放入 Store

❌ 错误做法：将组件内部的私有数据（如弹窗显示状态、表单临时输入值）放入 Store 中，导致 Store 臃肿，增加维护成本。

✅ 正确做法：只有需要在多个组件之间共享的数据（如用户信息、购物车数据、全局配置），才放入 Store 中；组件私有数据，使用组件内的 ref/reactive 即可。

## 六、Pinia 与 Vuex 全面对比（为什么选择 Pinia？）

很多开发者会纠结于选择 Pinia 还是 Vuex，下面通过表格对比两者的核心特性，帮你明确选择 Pinia 的理由：

|特性|Pinia|Vuex|
|---|---|---|
|官方推荐|✅ Vue3 官方首选，Vue 团队维护|❌ Vue3 中已被 Pinia 替代，不再推荐新项目使用|
|Mutations|❌ 无，同步/异步逻辑统一在 Actions 中|✅ 必须通过 Mutations 修改 State，同步/异步分离|
|Modules|❌ 扁平化设计，每个 Store 独立，无需嵌套|✅ 需要嵌套 Modules 实现模块化，结构繁琐|
|TypeScript 支持|✅ 原生支持，类型推导完善，自动补全友好|❌ 需额外配置类型，类型映射复杂，体验较差|
|体积|✅ \~1KB（gzip 压缩），轻量无负担|❌ \~10KB（gzip 压缩），体积较大|
|Vue3 适配|✅ 完美适配组合式 API，原生支持 reactivity|❌ 适配较差，需使用 vuex@4 版本，API 繁琐|
|Devtools 集成|✅ 完美集成，支持状态追踪、时间旅行|✅ 支持，但调试体验不如 Pinia|
|上手难度|✅ 低，API 简洁，无需记忆复杂概念|❌ 高，需记忆 mutations、actions、modules 等概念|

总结：对于 Vue3 项目，无论是新项目还是 Vue2 迁移项目，都优先选择 Pinia；Vue2 项目如果不需要 TypeScript，也可以使用 Pinia（兼容 Vue2），或继续使用 Vuex，但不推荐新项目使用 Vuex。

## 七、Pinia 最佳实践（规范开发，提升可维护性）

在实际项目中，遵循以下最佳实践，可以让 Pinia 的使用更规范、更易维护，尤其适合中大型项目。

1. 按业务模块拆分 Store：每个业务模块对应一个 Store（如 user、cart、order、setting），避免一个 Store 包含所有状态，导致臃肿难维护。

2. 统一 Store 目录结构：在 src 目录下创建 stores 文件夹，每个 Store 单独创建一个文件（如 user\.ts、cart\.ts），并在 stores 目录下创建 index\.ts，统一导出所有 Store，方便组件引入。

3. 优先使用组合式写法：Vue3 项目推荐使用组合式写法，与 \&lt;script setup\&gt; 风格统一，代码更简洁、更灵活，也更易适配 TypeScript。

4. 使用 storeToRefs 进行响应式解构：避免直接解构导致的响应式丢失，养成良好的编码习惯。

5. 异步逻辑统一放在 Actions 中：无论同步还是异步逻辑，都集中在 Actions 中处理，便于调试、维护和复用，避免在组件中直接修改全局状态。

6. 合理使用状态持久化：只对需要持久化的状态（如用户 token、购物车数据）开启持久化，避免不必要的存储占用。

7. 避免 Store 嵌套调用过多：跨 Store 通信尽量简洁，避免多个 Store 互相调用，导致依赖关系复杂，可通过事件总线或全局状态中转。

8. 组件卸载时取消 Store 订阅：使用 $subscribe 订阅状态变化后，在组件卸载时调用取消订阅方法，避免内存泄漏。

## 八、总结

Pinia 作为 Vue3 官方推荐的新一代状态管理库，以其极简的 API、完善的 TypeScript 支持、轻量高效的性能、灵活的模块化设计，彻底解决了 Vuex 在 Vue3 时代的痛点，成为 Vue 项目状态管理的首选方案。

本文从 Pinia 的核心优势、核心概念、快速上手、进阶用法、踩坑指南、与 Vuex 对比、最佳实践等多个维度，进行了全面、详细的总结，覆盖了从新手入门到实战进阶的所有核心知识点。无论是新手学习 Pinia，还是开发者在项目中使用 Pinia 进行实战，都可以将本文作为参考，快速掌握 Pinia 的使用技巧，规范开发流程，提升项目可维护性。

最后，Pinia 的学习成本很低，只要掌握了 Vue3 的基本语法，就能快速上手，建议大家多动手实践，将 Pinia 运用到实际项目中，真正体会它带来的开发效率提升。

