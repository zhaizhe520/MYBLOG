---
title: live2D的表情交互系統
date: 2026-05-07 17:28:15
tags: 表情交互系統
excerpt: 表情交互系統
categories: 
    - 項目
    - 系統
---
```
import { usePetStore } from '@/stores/petStore'
const petStore = usePetStore()
const componentId = 'nav-home' // 每個DOM給一個唯一的 ID
```

綁定的完整流程
指令觸發：用戶鼠標指到 `<div v-pet-tip:nav-home:"你好">老婆</div>`

ID 傳遞：nav-home 被傳送到 petStore。

邏輯映射：Store 根據 nav-home 查找對應的動作關鍵字 TapGreeting。

驅動模型：PixiJS 監聽到關鍵字，去模型的 JSON 文件 裡找到 motions/happy.motion3.json 並播放。