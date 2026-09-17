---
title: Galgame 引擎架構
date: 2026-05-07 10:11:44
tags: vue3組件插入Galgame
excerpt: Galgame 引擎架構
categories:
    - 項目
    - 組件
---
# 數據結構設計 (Schema)
首先定義劇本格式，建議將劇本抽離成 JSON 檔案，方便後續擴充。

```
// src/data/chapter1.json
{
  "scenes": [
    {
      "id": "start",
      "background": "/bg/classroom.jpg",
      "character": { "name": "艾莉絲", "image": "/char/alice_happy.png" },
      "dialogue": "今天天氣真好，要一起去圖書館嗎？",
      "sound": "/se/click.mp3",
      "nextId": "choice_1"
    },
    {
      "id": "choice_1",
      "type": "choice",
      "dialogue": "該怎麼回答她呢？",
      "options": [
        { "text": "好啊，走吧！", "targetId": "path_a", "points": { "alice": 10 } },
        { "text": "抱歉，我要回去打遊戲。", "targetId": "path_b", "points": { "alice": -5 } }
      ]
    }
  ]
}S
```
# 狀態管理 (Pinia Store)
使用 Pinia 來管理遊戲狀態（存檔、好感度、目前進度），這樣你在任何組件都能存取。

```

// src/stores/game.js
import { defineStore } from 'pinia';

export const useGameStore = defineStore('game', {
  state: () => ({
    currentSceneId: 'start',
    history: [], // 用於回看對話紀錄 (Backlog)
    stats: { alice: 0 }, // 好感度數值
    isAuto: false, // 自動播放
    isTyping: false // 是否正在打字機動畫中
  }),
  actions: {
    moveTo(id) {
      this.currentSceneId = id;
    }
  }
});
```
# 核心組件開發：打字機效果 (Typewriter)
Galgame 的靈魂在於文字逐字出現。我們可以用一個 Composable 來處理。

```
// src/composables/useTypewriter.js
import { ref } from 'vue';

export function useTypewriter() {
  const displayedText = ref('');
  let timer = null;

  const type = (fullText, speed = 50) => {
    displayedText.value = '';
    clearInterval(timer);
    let i = 0;
    timer = setInterval(() => {
      if (i < fullText.length) {
        displayedText.value += fullText.charAt(i);
        i++;
      } else {
        clearInterval(timer);
      }
    }, speed);
  };

  const skip = (fullText) => {
    clearInterval(timer);
    displayedText.value = fullText;
  };

  return { displayedText, type, skip };
}

```
# 畫面佈局架構 (App.vue / GameView.vue)
```
<template>
  <div class="stage">
    <!-- 背景層 -->
    <transition name="fade">
      <img :key="currentScene.background" :src="currentScene.background" class="bg" />
    </transition>

    <!-- 立繪層 -->
    <transition name="slide-fade">
      <img v-if="currentScene.character" 
           :key="currentScene.character.image" 
           :src="currentScene.character.image" 
           class="character-sprite" />
    </transition>

    <!-- 對話框區域 -->
    <div class="ui-layer" @click="handleScreenClick">
      <div v-if="currentScene.type === 'choice'" class="choices">
        <button v-for="opt in currentScene.options" 
                @click.stop="makeChoice(opt)">
          {{ opt.text }}
        </button>
      </div>

      <div class="message-box">
        <span class="name-tag">{{ currentScene.character.name }}</span>
        <p class="text">{{ displayedText }}</p>
      </div>
    </div>
  </div>
</template>
```
# 立项开发清单 (Roadmap)

| 阶段 | 阶段重点 | 核心任务 |
| ---- | -------- | -------- |
| 第一阶段 | 核心渲染实现 | 点击切换、背景/立绘显示、打字机文字效果 |
| 第二阶段 | 分支系统实作 | 「选项按钮」与跳转逻辑 |
| 第三阶段 | 资源管理 | 预加载图片与音频，防止切换时出现白屏或延迟 |
| 第四阶段 | UI/UX | 增加 Log（回看）、Skip（快进）、Save/Load（存档）功能 |
| 第五阶段 | 特效 | 画面震动（遇到冲击）、淡入淡出、BGM 循环播放 |