---
title: Vue組件的應用
date: 2026-04-21 16:39:35
tags: Vue組件的應用
categories:
  - Vue框架
---
# 基本
```
export default {
  data() {},       // 数据、变量、开关
  methods: {},     // 点击事件、函数
  components: {},  // 子组件
  props: {}        // 传值
}
```
# 點擊實現dom居中
```
<template>
  <div>
    <!-- 点击这个 div 弹出 -->
    <div class="click-box" @click="showModal = true">
      点我弹出中间组件
    </div>

    <!-- 居中弹出层 -->
    <div class="modal-mask" v-if="showModal" @click="showModal = false">
      <div class="modal-content" @click.stop>
        <h3>我在页面正中间</h3>
        <p>这是你要的居中组件</p>
        <button @click="showModal = false">关闭</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showModal: false
    }
  }
}
</script>

<style scoped>
/* 点击区域样式 */
.click-box {
  padding: 10px 20px;
  background: #eee;
  cursor: pointer;
  display: inline-block;
  margin: 20px;
}

/* 遮罩全屏覆盖 */
.modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

/* 中间内容框 */
.modal-content {
  background: white;
  padding: 30px;
  border-radius: 8px;
  min-width: 300px;
  text-align: center;
}
</style>
```

# 同組件內部 狀態驅動內容切換

```
<template>
  <div class="page-container">

    <!-- ====================== 第1頁內容 ====================== -->
    <div v-if="currentPage === 1" class="content">
      1
    </div>

    <!-- ====================== 第2頁內容 ====================== -->
    <div v-if="currentPage === 2" class="content">
      2
    </div>

    <!-- ====================== 第3頁內容 ====================== -->
    <div v-if="currentPage === 3" class="content">
      3
    </div>

    <!-- ====================== 第4頁內容 ====================== -->
    <div v-if="currentPage === 4" class="content">
      4
    </div>

    <!-- ====================== 底部左右切換按鈕 ====================== -->
    <div class="bottom-buttons">
      <button @click="prevPage" :disabled="currentPage === 1">← </button>
      <button @click="nextPage" :disabled="currentPage === 4"> →</button>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      currentPage: 1 // 控制現在顯示第幾頁
    }
  },
  methods: {
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--
      }
    },
    nextPage() {
      if (this.currentPage < 4) {
        this.currentPage++
      }
    }
  }
}
</script>

<style scoped>
.page-container {
  width: 100%;
  height: 80vh;
}

.content {
  font-size: 24px;
}

.bottom-buttons {
  width: 100%;
  position: fixed;
  bottom: 20px;
  display: flex;
  justify-content: center;
  gap: 20px;
}

button {
  padding: 10px 20px;
  font-size: 18px;
  cursor: pointer;
}
</style>
```
# 彈窗組件服用導致的數據不同步解決辦法/數據stale(改同一个組件form值)
<div style="color:pink">form 就是一个【专属数据盒子】</div>

```
父组件
  ↓ 传不同数据
共用 1 个弹窗组件
  ↓ 弹窗内部
    form = { 图片, 标题, 内容 }
    打开 → 刷新 form

```

Vue 弹窗组件复用导致数据不同步（数据 stale）解决方案

弹窗复用是 Vue 项目最常见的优化手段，但也是数据 stale（旧数据残留）、不同步的核心原因：弹窗组件只创建一次，关闭时没有销毁实例，旧数据会残留在组件内，下次打开就会展示错误数据。
我给你整理了最实用、最常用的 4 种解决方案，按推荐优先级排序，直接套用即可解决问题。
核心原因先搞懂
弹窗用了 v-show / 控制 visible 显示隐藏 → 组件实例不销毁
组件内的 data、props、表单值 会保留上一次的状态
下次打开弹窗，传入新数据，但旧数据没清空 → 数据不同步

## <div style="color:pink">方案 1：强制销毁弹窗（最简单、最稳）</div>

```
直接让弹窗关闭时销毁组件，下次打开重新创建，彻底杜绝数据残留。
用法
把 v-show 改成 v-if
vue
<!-- 错误：复用组件，数据残留 -->
<Dialog v-show="visible" />

<!-- 正确：关闭销毁，打开重建 → 永远无旧数据 -->
<Dialog v-if="visible" />
优点
零成本、一行代码解决
100% 避免数据 stale
适合简单弹窗
缺点
频繁开关会有轻微性能损耗（普通项目完全无感）
```


<div style="color:pink">方案 2：监听 props / 打开事件刷新数据（推荐 🌟）</div>

你打开弹窗 → props.visible 从 false → true

不销毁组件，只刷新数据，性能最优，企业项目最常用。
适合：弹窗需要复用、频繁开关、不想重建 DOM 的场景。
```
步骤 1：父组件打开弹窗时传值
vue
<!-- 父组件 -->
<Dialog 
  :visible="visible" 
  :form-data="formData"  <!-- 每次打开传新数据 -->
  @close="visible = false"
/>
步骤 2：子组件监听数据 /visible，强制刷新
vue
<!-- 子组件 Dialog.vue -->
<script setup>
import { watch, ref } from 'vue'
const props = defineProps(['visible', 'formData'])
const form = ref({})

// 方案 A：监听 visible，打开时重置/更新数据
watch(() => props.visible, (newVal) => {
  if (newVal) {
    // 打开弹窗 → 重置表单/刷新数据
    form.value = { ...props.formData } // 解构避免引用污染
  } else {
    // 关闭弹窗 → 清空数据
    form.value = {}
  }
})
```

```
// 方案 B（进阶）：直接监听传入的 formData 自动同步
watch(() => props.formData, (newData) => {
  if (newData) form.value = { ...newData }
}, { deep: true })
</script>

```

<div style="color:pink">方案 3：关闭弹窗时手动重置所有数据（通用）</div>
在关闭 / 取消方法里，手动把所有数据恢复成初始值。
适合：不想用 v-if、也不想写 watch 的场景。

```
vue
<script setup>
import { ref } from 'vue'
const emit = defineEmits(['close'])

// 1. 定义初始数据模板
const initialForm = { name: '', age: null, id: '' }
const form = ref({ ...initialForm })

// 2. 关闭时重置数据
const handleClose = () => {
  form.value = { ...initialForm } // 重置为初始值
  emit('close')
}
</script>
✅ 优点：可控性强✅ 优点：不会销毁组件，性能好
```

<div style="color:pink">方案 4：使用 key 强制刷新组件（高级技巧）</div>

如果你必须用 v-show 复用弹窗，但又想强制刷新数据，用 key。
Vue 遇到不同 key 会重建组件。
```
vue
<Dialog 
  v-show="visible" 
  :key="visible ? Date.now() : 'hidden'" 
  :data="currentData"
/>
每次打开 key 变化 → 组件重建 → 数据全新。
最容易踩的坑（必看）
对象引用污染
js
// 错误：直接赋值，会修改父组件数据
form.value = props.formData

// 正确：解构/深拷贝，断开引用
form.value = { ...props.formData }
只隐藏不清理
只用 visible 控制显示隐藏，不重置 data → 必 stale
异步请求未取消
弹窗关闭后，接口返回覆盖数据 → 用 watch 清理异步请求
```

# 快速选择指南

简单弹窗、快速解决	方案 1：v-if
频繁开关、性能优先	方案 2：watch 监听
不想用监听、手动控制	方案 3：关闭重置数据
必须复用但要刷新	方案 4：key 刷新
--------------------------
# 总结
数据 stale 根源：弹窗复用 → 实例不销毁 → 旧数据残留
最简单解法：v-if 替换 v-show
最优企业解法：watch 监听 visible / props 刷新数据
关键细节：对象一定要拷贝，避免引用同步污染

# 實例
 watch、props 复杂用法
<div style="color:pink">組件的傳值</div>
父傳子

```
父組件裡面

子組件:xxx="acc";穿
 
 export default{
  data(){
    return{
      acc:
    }
  }
 }

 子組件裡面接收
 數組:
 export default{
  props:["xxx"]
 }
對象:
 export default{
  props:{
    acc
  }
 }
 ```

子傳父
# 自定義事件
 子传父
 
  
```
子組件要做的是定義一個組件
  emit子 emit('事件名', 数据)

  父組件
  <child @事件名 >

```


# 兄弟組件之間的傳值
<div style="color:pink">方法一</div>
借父组件中转（最基础、面试必问、新手首选）
原理：兄弟 A → 父组件 → 兄弟 B
A 子传父（emit）把数据给爷爷父组件
父组件存数据
父组件 props 再传给另一个兄弟 B
