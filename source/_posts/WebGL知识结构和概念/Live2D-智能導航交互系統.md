---
title: Live2D 智能導航交互系統
date: 2026-05-06 21:12:35
tags: Live2D 智能導航交互系統
excerpt: Live2D 智能導航交互系統
categories: 
    - 項目
    - 系統
---
```
交互細節：除了顯示文字，是否需要 Live2D 模型執行特定的 Motion（動作） 或 Expression（表情）？（例如：滑鼠移入「檔案下載」時，模型做出「驚訝」的動作）。
語音/音效：是否需要同步觸發語音播放？
自動消失：是否需要設置 setTimeout 讓對話在一段時間後自動關閉，而不僅僅依賴 mouseleave？
優先級系統：如果同時有多個事件觸發（例如背景系統通知 vs 導航列 Hover），誰的權限更高？
```
# 實現邏輯
```
  <div 
    class="nav-item"
    @mouseenter="petStore.showPet('回到首頁查看最新消息！', componentId)"
    @mouseleave="petStore.hidePet(componentId)"
  >
    首頁
  </div>
  ```

## 技術棧建議
框架: Vue 3 (Composition API)

狀態管理: Pinia (推薦，適合管理全局的對話內容) 或 mitt (輕量級事件工具)

動畫庫: GSAP 或 CSS Transition (用於氣泡的淡入淡出)

字體打字邏輯: 自定義 Composable (Hook)

##  核心邏輯架構
A. 全局對話調度中心 (Pinia Store)
不要讓文字組件直接去控制 Live2D 組件，而是透過一個「中介者」。

State: 儲存當前對話文字 currentText、氣泡可見性 isVisible。

Actions: showMessage(text) 和 hideMessage()。

B. 觸發組件 (你的文字或 Div)
在這些組件上綁定 @mouseenter 事件：

滑鼠移入時，調用 Store 的 showMessage('這是對應的說明文字')。

滑鼠移出時 (@mouseleave)，調用 hideMessage()。

C. Live2D 氣泡組件 (接收端)
這是最關鍵的部分，它需要監聽 Store 的變化：

監聽變化: 使用 watch 監聽 Store 中的 currentText。

打字機效果: 當 currentText 改變時，觸發一個迴圈函數，逐字將字串填入一個本地的 displayingText 響應式變數中。

氣泡定位: 氣泡組件使用 position: absolute 定位在 Live2D 看板娘的頭部附近。

##  關鍵代碼邏輯示意
打字機核心邏輯 (useTypewriter.js):
```
export function useTypewriter() {
  const output = ref('');
  let timer = null;

  const type = (text, speed = 60) => {
    output.value = ''; // 清空舊文字
    let i = 0;
    clearInterval(timer);
    timer = setInterval(() => {
      if (i < text.length) {
        output.value += text.charAt(i);
        i++;
      } else {
        clearInterval(timer);
      }
    }, speed);
  };

  return { output, type };
}
```
要實現這種「跨組件通訊」並觸發「打字機氣泡」的效果，在 Vue 3 中最優雅的方式是使用 事件匯流排 (Event Bus) 或 全局狀態管理 (Pinia)。

這裡我為你拆解具體的技術棧與邏輯架構：

1. 技術棧建議
框架: Vue 3 (Composition API)

狀態管理: Pinia (推薦，適合管理全局的對話內容) 或 mitt (輕量級事件工具)

動畫庫: GSAP 或 CSS Transition (用於氣泡的淡入淡出)

字體打字邏輯: 自定義 Composable (Hook)

核心邏輯架構
1.全局對話調度中心 (Pinia Store)
不要讓文字組件直接去控制 Live2D 組件，而是透過一個「中介者」。

State: 儲存當前對話文字 currentText、氣泡可見性 isVisible。

Actions: showMessage(text) 和 hideMessage()。

2.觸發組件 (你的文字或 Div)
在這些組件上綁定 @mouseenter 事件：

滑鼠移入時，調用 Store 的 showMessage('這是對應的說明文字')。

滑鼠移出時 (@mouseleave)，調用 hideMessage()。

3.Live2D 氣泡組件 (接收端)
這是最關鍵的部分，它需要監聽 Store 的變化：

監聽變化: 使用 watch 監聽 Store 中的 currentText。

打字機效果: 當 currentText 改變時，觸發一個迴圈函數，逐字將字串填入一個本地的 displayingText 響應式變數中。

氣泡定位: 氣泡組件使用 position: absolute 定位在 Live2D 看板娘的頭部附近。

關鍵代碼邏輯示意
```
打字機核心邏輯 (useTypewriter.js):

JavaScript
export function useTypewriter() {
  const output = ref('');
  let timer = null;

  const type = (text, speed = 60) => {
    output.value = ''; // 清空舊文字
    let i = 0;
    clearInterval(timer);
    timer = setInterval(() => {
      if (i < text.length) {
        output.value += text.charAt(i);
        i++;
      } else {
        clearInterval(timer);
      }
    }, speed);
  };

  return { output, type };
}
```
##  氣泡組件佈局 (Bubble.vue):

使用 CSS 三角形技巧 或 SVG 製作氣泡的小尾巴。

使用 `<Transition> `包裹氣泡，實現 opacity 和 transform: translateY 的流暢切換


# 開始實現


pinia基本框架
```

// store/petStore.js
export const usePetStore = defineStore('pet', {
  state: () => ({
    message: '',
    isVisible: false
  }),
  actions: {
    // 封裝一個統一的進入方法
    onHover(text) {
      this.message = text;
      this.isVisible = true;
    },
    // 封裝一個統一的離開方法
    onLeave() {
      this.isVisible = false;
      this.message = '';
    }
  }
})
```

```
// src/composables/useTypewriter.js
//實現打字效果單獨js
import { ref } from 'vue'

export function useTypewriter() {
  const displayText = ref('')
  let timer = null

  const startTyping = (text, speed = 50) => {
    displayText.value = ''
    clearInterval(timer)
    let i = 0
    timer = setInterval(() => {
      if (i < text.length) {
        displayText.value += text.charAt(i)
        i++
      } else {
        clearInterval(timer)
      }
    }, speed)
  }

  return { displayText, startTyping }
}
```


<div style="color:pink">pet組件打字效果</div>

```
// 1. 引入你的打字機 js
import { useTypewriter } from '../composables/useTypewriter'

const petStore = usePetStore()
const { message, isVisible } = storeToRefs(petStore)

// 2. 初始化打字機功能
const { displayText, startTyping } = useTypewriter()
import { watch } from "vue"
// 3. 核心邏輯：監聽 Pinia 裡的文字變化
watch(message, (newText) => {
  if (newText) {
    // 當 Pinia 收到新文字時，啟動打字效果
    startTyping(newText, 60) // 60ms 是打字速度
  } else {
    // 如果文字被清空，也可以同步清空顯示內容
    // displayText.value = '' 
  }
})

```
# 解構會弄丟響應性

`import { storeToRefs } from 'pinia'`

它不是你定義的，是 Pinia 附贈的工具。

只要你想從 Store 中解構 (Destructure) 出數據來用，就請記得帶上它。

如果是 Store 裡的 Actions (函式)，則不需要它，直接解構即可。

<div style="color:red">為什麼 Pinia 要提供這個東西？</div>

這跟 Vue 3 的響應式原理有關。

Store 本質上是一個 Reactive 物件：Pinia 的 Store 就像是一個巨大的 reactive() 物件。

解構會破壞響應式：在 JavaScript 中，如果你對一個物件進行解構（例如 const { message } = store），你拿到的只是一個普通的字串或數值。這就像是把一張活的照片印成了紙本，照片裡的人之後怎麼動，你手上的那張紙都不會變。

storeToRefs 的作用：它會幫你把 Store 裡的數據重新包裝成 ref。這樣你解構出來的變數就變成了「活的」連接點，Store 裡的值一變，你的變數也會跟著變。

<div style="color:red">為什麼解構會「弄丟」響應性？</div> 
在 Vue 3 的底層邏輯中，state 是一個被 reactive 包裹的物件。

錯誤操作： 如果你在組件裡寫 const { message } = usePetStore()，這就像是把 message 的當前值（例如空字串）複製給了一個新變數。

後果： 之後當你在導航欄 hover，讓 Store 裡的 message 變成「你好」時，你組件裡那個變數還停留在「空字串」，因為它已經跟原始物件斷開連結了。

# 3种方法實現mouse交互
每個導航項或按鈕都要手動寫 mouseenter、mouseleave 以及傳入 componentId，代碼確實會變得很冗長且難以維護。
##  方法一：封裝成自定義指令 (Directive) —— 最推薦
你可以創建一個 v-pet-tip 指令，這樣你只需要在元素上寫一句話，剩下的邏輯（包含自動生成 ID）都交給指令處理。

定義指令 (例如在 main.js 或單獨的檔案)：
```
// v-pet-tip 指令
const vPetTip = {
  mounted(el, binding) {
    const petStore = usePetStore()
    // 使用元素的文本或隨機數作為唯一 ID
    const instanceId = Math.random().toString(36).substring(2, 9)
    
    el._handleEnter = () => petStore.showPet(binding.value, instanceId)
    el._handleLeave = () => petStore.hidePet(instanceId)

    el.addEventListener('mouseenter', el._handleEnter)
    el.addEventListener('mouseleave', el._handleLeave)
  },
  unmounted(el) {
    el.removeEventListener('mouseenter', el._handleEnter)
    el.removeEventListener('mouseleave', el._handleLeave)
  }
}
```
在組件中使用：
現在你只需要一行代碼，不需要管理 ID，也不需要寫兩個事件：

```
<div v-pet-tip="'回到首頁查看最新消息！'">首頁</div>
<div v-pet-tip="'看看我的個人作品集'">關於我</div>
```

## 方法二：封裝成通用的「交互組件」
如果你不習慣用指令，可以寫一個封裝組件 PetTooltip.vue，把 div 包起來。

```
<script setup>
import { usePetStore } from '@/store/petStore'
const props = defineProps(['text'])
const petStore = usePetStore()
const id = Symbol('pet-tip') // 使用 Symbol 產生絕對唯一的 ID
</script>

<template>
  <div @mouseenter="petStore.showPet(text, id)" @mouseleave="petStore.hidePet(id)">
    <slot></slot> <!-- 這裡放原本的導航內容 -->
  </div>
</template>

```
使用方法：

```
<PetTooltip text="回到首頁！">
  <div class="nav-item">首頁</div>
</PetTooltip>

```

##  方法三：利用事件委託 (Event Delegation)
如果你所有的導航項都在同一個父容器下，你可以把監聽器綁在父層，透過 data-attribute 來獲取文字。

```
<div class="nav-container" @mouseenter.capture="handleNavHover" @mouseleave.capture="handleNavLeave">
  <div class="nav-item" data-tip="回到首頁！">首頁</div>
  <div class="nav-item" data-tip="了解更多細節">關於</div>
</div>
```

JS:
```
const handleNavHover = (e) => {
  const tip = e.target.getAttribute('data-tip')
  if (tip) {
    // 這裡可以使用 e.target 作為唯一的 ID 參考
    petStore.showPet(tip, e.target)
  }
}

const handleNavLeave = (e) => {
  petStore.hidePet(e.target)
}
```
