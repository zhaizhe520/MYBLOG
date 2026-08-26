---
title: Vue3 + Live2D 站娘
date: 2026-05-05 22:32:30
tags: Vue3 + Live2D 
excerpt: Vue3 + Live2D 
categories:
    - 項目
---

# 📝 项目立项书：基于 Vue3 的看板娘交互组件

1. 项目背景与目标
背景： 传统的网页过于死板，缺乏生命力。为了提升用户交互体验，决定开发一个基于 Vue3 的桌面宠（看板娘）组件。

目标：

实现 Live2D 模型的高性能渲染。

支持鼠标追踪、点击反馈、随机语音/气泡对话。

组件化封装，支持在任何 Vue3 项目中“一键引入”。

2. 技术选型 (Tech Stack)
框架： Vue 3 (Composition API)

渲染引擎： PixiJS v7+ (强大的 WebGL 渲染库)

核心插件： pixi-live2d-display (连接 PixiJS 与 Live2D 的桥梁)

构建工具： Vite (极速的热更新体验)

动画库： GSAP (用于处理气泡弹出、UI 平滑位移)

-----------------------------------------------

# 🛠️ 技术栈的构成（以你的项目为例）
一个完整、专业的 Live2D 宠物组件技术栈通常分为以下几个层次：

1. 前端框架 (The Framework)
选项： Vue 3

作用： 它是你的“总指挥部”。负责组件的生命周期管理（模型什么时候加载、什么时候销毁）、数据绑定（宠物的心情、坐标）以及 UI 交互逻辑。

2. 渲染层 (The Engine)
选项： PixiJS

作用： 这是宠物的“视网膜和肌肉”。Live2D 模型需要高性能的 WebGL 渲染，PixiJS 是目前最轻量且强大的 2D 渲染引擎，能保证宠物动作丝滑不卡顿。

3. 核心中间件 (The Bridge)
选项： pixi-live2d-display + Cubism Core

作用： 这是“翻译官”。Live2D 官方的模型数据（.moc3） PixiJS 默认看不懂，这个插件专门负责解析模型文件并将其绘制在 PixiJS 的画布上。

4. 构建工具 (The Builder)
选项： Vite

作用： 现代化的开发环境。负责代码的实时热更新、资源打包和性能优化。

5. 辅助工具 (Auxiliary Tools)
选项： GSAP (动画库) 或 Pinia (状态管理)

作用：

GSAP： 让宠物对话框弹出、位置移动更符合物理逻辑。

Pinia： 如果你的宠物数据要在多个页面共享，它负责存储宠物的“记忆”。

----------------------------------------------------
没问题，这是一份为你量身定制的 “环境初始化清单”。环境搭建与依赖配置。

🛠️ 第一步：初始化 Vue3 项目
如果你还没有创建项目，先用 Vite 快速起步：
```
Bash
# 创建项目
輕量
npm create vite@latest my-live2d-pet -- --template vue
完整
npm create vue@latest
```

# 进入目录
```
cd my-live2d-pet
```

# 安装基础依赖
```
npm install
```
📦 第二步：安装核心依赖
这是实现 Live2D 效果最关键的三件套：

```
Bash
# 1. 安装渲染引擎 PixiJS（建议使用 v7 版本，文档最全）
npm install pixi.js@7
```

```
# 2. 安装 Live2D 解析插件
npm install pixi-live2d-display
```

# 3. 安装官方核心运行时（Cubism Core）
# 注意：由于版权原因，这个包通常需要手动引入，或者安装社区维护的版本

```
npm install live2dcubismcore
```

`
Live2DCubismCore 是解析 .model3.json 的二進制黑盒
`


<div style="color:pink">核心官網</div>

`https://www.live2d.com/zh-CHS/sdk/download/web/`

<div style="color:pink">Cubism Core for Web</div>

<div style="color:pink">不是Cubism SDK for Web</div>

```
簡單來說，Cubism Core 是引擎的「心臟」（底層解析邏輯），而 Cubism SDK 是外層的「軀幹」（各種封裝好的 API 和工具類）。如果只加載了 SDK 而沒有 Core，或者 Core 的版本與模型不匹配，就會出現你遇到的 read(0) 報錯，因為 SDK 根本無法解析模型數據。

```



<div style="color:pink">live2dcubismcore.min.js</div>

pixi-live2d-display	你目前選用的第三方適配庫，它需要 Cubism Core 才能工作。(npm 包)


🌐 在 index.html 引入核心库
在 src/main.js 加载之前，我们需要确保 Live2D 的底层核心库已经加载。在 index.html 的 <head> 中添加：

```

HTML
<!-- 引入 Live2D Cubism SDK 核心文件 -->
<script src="https://cubism.live2d.com/sdk-web/bin/cubismcore/live2dcubismcore.min.js"></script>
```



# 反編譯 拿模型

.lpk 加密格式反编译，提取出标准的 .moc3 和 .json 资源。

```
https://github.com/ihopenot/LpkUnpacker
```

📂 第三步：静态资源配置（关键环节）
Live2D 模型包含很多 .json、.moc3 和图片文件，这些必须放在 public 目录下，否则 Vite 会找不到它们。
```
建议的目录结构：（你解包壓縮的文件在哪裡就在哪裡）json文件不要動
Plaintext
public/
└── live2d/
|    └── xxx/             
|        ├── xxx.model3.json
|        ├── xxx.moc3
|        ├── xxx.png
|      
├── src/
│   └── components/
│       └── Live2dPet.vue      # 你的宠物组件
└── index.html
```

🏁 第五步：验证安装
为了确认你的环境已经配好，可以在 App.vue 中尝试打印一下对象：

JavaScript
import * as PIXI from 'pixi.js';
import { Live2DModel } from 'pixi-live2d-display';

console.log('Pixi版本:', PIXI.VERSION);
console.log('Live2D模型类:', Live2DModel);



# 技術棧介紹

## PixiJS 是一款效能極強的開源 2D 渲染引擎。

如果你想在網頁上製作流暢的動畫、遊戲或是處理大量的圖像，PixiJS 幾乎是業界的首選。它最大的特色在於「快」，利用 WebGL（以及現代的 WebGPU）來達成硬體加速，讓你在瀏覽器中同時渲染成千上萬個物件還能保持 60 FPS。

以下是關於 PixiJS v7 的核心介紹：

1.它不是遊戲引擎，而是渲染引擎
這是一個常見的誤區。PixiJS 不包含物理引擎、碰撞偵測或關卡編輯器（雖然可以搭配其他工具實現）。它專注於一件事：把圖形放到螢幕上，並且快到不可思議。

與 Three.js 相比： Three.js 專攻 3D，而 PixiJS 專攻 2D。

與 Canvas API 相比： 直接寫 Canvas 很痛苦且效能有限；PixiJS 封裝了複雜的 WebGL 調用，讓你用直觀的物件導向方式操作。

2.PixiJS v7 的核心優勢
雖然目前 PixiJS 已進入 v8 版本，但 v7 版本 確實如你所說，是目前文件最齊全、社群套件支援最穩定的版本。

極致效能： 自動處理批次渲染（Batch Rendering），減少 CPU 與 GPU 之間的溝通次數。

靈活的場景樹 (Scene Graph)： 使用類似 Flash 或 DOM 的結構（Container, Sprite, Graphic），讓你輕鬆管理層級關係。

強大的濾鏡系統 (Filters)： 內建多種視覺特效（模糊、發光、置換貼圖等），並支援自定義 Shader。

跨平台支援： 無縫支援桌機與行動裝置瀏覽器。

3.核心概念圖解
理解 PixiJS 的運作邏輯，主要環繞在以下三個層次：

Application (應用程式)： 總管，負責初始化渲染器、計時器和最頂層的容器。

Stage (舞台)： 所有的視覺物件都必須放在舞台（根容器）裡。

Container & Sprite (容器與精靈)：

Sprite 是最基本的影像單位（如一張圖片）。

Container 則像資料夾，可以群組化物件，同步進行縮放、旋轉或位移。

4.為什麼選擇 v7？
文檔與範例： 官方範例（PixiJS Examples）對 v7 的支援極為完整，幾乎所有教學都能直接運行。

API 穩定性： 許多知名的遊戲框架（如 Phaser）或開發工具，底層對 v7 的相容性最高。

TypeScript 支援： v7 對 TypeScript 有非常完美的定義，開發體驗極佳，能有效避免拼錯屬性的低級錯誤。

```
import { Application, Sprite } from 'pixi.js';

// 1. 建立應用程式
const app = new Application({ width: 800, height: 600 });
document.body.appendChild(app.view);

// 2. 載入並建立精靈
const bunny = Sprite.from('https://pixijs.com/assets/bunny.png');

// 3. 設定屬性並加入舞台
bunny.anchor.set(0.5); // 設定中心點
bunny.x = app.screen.width / 2;
bunny.y = app.screen.height / 2;
app.stage.addChild(bunny);

// 4. 動畫迴圈
app.ticker.add((delta) => {
    bunny.rotation += 0.1 * delta; // 讓兔子轉起來
});

```
-----------------------------

1. GSAP 是什麼？
GSAP (GreenSock Animation Platform) 是一個專門用來處理屬性動畫的 JavaScript 庫。

簡單來說：只要是一個「數字」，GSAP 就能讓它平滑地從 A 變到 B。

HTML 元素： 讓一個按鈕從左邊滑到右邊。

PixiJS 物件： 讓叢雨的透明度（Alpha）從 0 變成 1。

數值： 讓一個計分板的數字從 0 快速跳動到 1000。

2. 為什麼有了 CSS 動畫還需要它？
你可能會想：「我用 CSS transition 或 keyframe 不也能做動畫嗎？」
GSAP 強大在於它解決了 CSS 做不到（或很難做）的事：

極致的控制權： 你可以隨時「暫停」、「倒放」、「放慢 0.5 倍」或「跳到動畫的第 2 秒」。這在做複雜 UI 互動時非常重要。

時間軸系統 (Timeline)： 這是它的靈魂。你可以安排：「先執行 A 動畫，執行到一半時開始跑 B，最後全部結束後再跑 C」。這不需要寫地獄般的 setTimeout。

效能卓越： 它的渲染效能極高，甚至在處理大量 SVG 動畫時比 CSS 還要流暢。

3. 在你的「叢雨專案」中，GSAP 可以用在哪？
雖然 PixiJS 本身有 app.ticker（每一幀執行的迴圈）可以寫動畫，但用 GSAP 會快很多：

UI 氣泡彈出： 當叢雨說話時，對話氣泡從縮小狀態（scale: 0）平滑地彈大（scale: 1），並帶有一點彈性效果 (Back.easeOut)。

平滑位移： 點擊按鈕，讓叢雨從畫面左邊緩緩滑動到右邊，而不是瞬間移動。

呼吸感： 雖然 Live2D 有內建呼吸，但如果你想讓整個模型有種輕微的上下浮動感，GSAP 一行代碼就能搞定。
## 插件

第一階段：誰負責什麼？（大藍圖）
在你的網頁裡，這三者是合作關係：

PixiJS (引擎)：它就像是一個畫布和畫家。它負責在瀏覽器上畫圖，並保證每秒畫 60 次（FPS），讓畫面看起來很順。

Live2D Cubism Core (大腦)：這是 Live2D 官方提供的核心黑盒子。它負責計算「當人物轉頭時，臉上的線條該怎麼變形」。

pixi-live2d-display (翻譯官)：這是最關鍵的插件。因為畫家（PixiJS）聽不懂大腦（Live2D Core）的專業術語，這個插件負責在兩者之間傳話，把計算好的變形結果交給畫家畫出來。

GSAP：提供絲滑的轉場與 UI 反饋（視覺潤飾）。

---------------------------------------
