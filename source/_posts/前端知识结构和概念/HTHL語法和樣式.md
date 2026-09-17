---
title: HTML语法和样式
date: 2026-04-15 18:47:38
tags: HTML項目集成
sticky: 5
excerpt: HTML項目集成
categories:
    - HTML
---
# 兄弟選擇器
```
父子 → 用 空格
css
.盒子:hover .文字 { }
```

```
兄弟 → 用 ~
css
.盒子:hover ~ .文字 { }
```

# 不透明度  一般搭配 :hover
`opacity:0;`
`opacity:1;`

# 移動

```
上下:transform: translateY(0); /* 初始位置 */
左右:transform: translateX(0); /* 初始位置 */
倾斜:transform: translate(左右, 上下);
放大縮小:transform: scale(数字);
旋轉:transform: rotate(角度deg);
transform: translate(0px, 0px) scale(0) rotate(0deg);
```
# flex佈局
## 父容器
1. 方向：flex-direction
决定横着排还是竖着排
```
css
flex-direction: row;           /* 默认 → 横着排（左到右）*/
flex-direction: column;         /* 竖着排（上到下）*/
flex-direction: row-reverse;    /* 横着反过来排 */
flex-direction: column-reverse; /* 竖着反过来排 */
```
2. 换行：flex-wrap
```
css
flex-wrap: nowrap;   /* 默认，不换行，挤在一起 */
flex-wrap: wrap;     /* 自动换行（最常用！）*/
```
3. 水平对齐：justify-content
```
css
justify-content: flex-start;    /* 左对齐（默认）*/
justify-content: flex-end;      /* 右对齐 */
justify-content: center;        /* 水平居中（最常用）*/
justify-content: space-between; /* 两端对齐，中间自动分 */
justify-content: space-around;  /* 每个子元素左右有间距 */
justify-content: space-evenly;  /* 间距全部相等（最好看）*/
```
4. 垂直对齐：align-items
```
css
align-items: flex-start;   /* 顶部对齐 */
align-items: flex-end;     /* 底部对齐 */
align-items: center;       /* 垂直居中（最常用！）*/
align-items: stretch;      /* 拉伸填满高度 */
```
5. 多行垂直对齐：align-content
换行后才用
```
css
align-content: center; /* 多行垂直居中 */
```

6. 主轴方向切换（几乎不用）
```
flex-flow = 方向 + 换行
css
flex-flow: row wrap;
```
## 子容器
子元素 3 个常用属性
1. flex: 1
占满剩余空间！
css
`flex: 1;`
两个子元素都写 flex:1 → 自动 50% 宽度
2. align-self
单独控制某个子元素垂直对齐
css
`align-self: center;`
3. order
调整顺序
css
`order: -1; /* 排最前面 */`

# class="名字 名字  " 

`同時用2個css`

# 邊框

```
border: 寬度 樣式 顏色;
border-radius: 0px; 圓角
    樣式
    /* 1. 無邊框 */
    border-style: none; 
    /* 2. 實線 */
    border-style: solid; 
    /* 3. 短虛線 */
    border-style: dashed; 
    /* 4. 點狀虛線 */
    border-style: dotted; 
    /* 5. 雙線 */
    border-style: double; 
    /* 6. 凹槽立體 */
    border-style: groove; 
    /* 7. 凸脊立體 */
    border-style: ridge; 
    /* 8. 內凹 */
    border-style: inset; 
    /* 9. 外凸 */
    border-style: outset; 
    /* 10. 隱藏邊框 */
    border-style: hidden; 


```

# 圖片導入與填充
## 圖片在容器裡面

```
.box img{
    width: 100%;
    height: 100%;
    object-fit: cover;
    object-position: center; /* 圖片居中裁切 */
}
```
## 背景圖片 background 全套（盒子背景圖）
```
.box{
    width: 400px;
    height: 300px;
    background-image: url("圖片.jpg");

    /* 圖片是否重複 */
    background-repeat: no-repeat; /* 不重複（預設會平鋪） */

    /* 圖片位置 水平 垂直 */
    background-position: center; /* 居中 */

    /* 圖片縮放 填滿容器（最重要！） */
    background-size: cover; /* 填滿、不變形、裁切 */
    background-size: contain;/*完整显示、不变形、不裁切*/
   	background-size: 200px 100px;/* 自定义宽高（固定尺寸）*/
    background-size: 100% 100%;/*相对容器百分比*/
    background-size: auto;/*原始大小*/
}
```
# 字體
<div style="color:pink">基础字体设置</div>

```
/* 字体族（优先顺序） */
font-family: "Microsoft YaHei", Arial, sans-serif;

/* 字体大小 */
font-size: 16px;
font-size: 1.2em;
font-size: 1rem;
font-size: larger;

/* 字体粗细 */
font-weight: normal;   /* 400 */
font-weight: bold;     /* 700 */
font-weight: lighter;
font-weight: bolder;
font-weight: 100~900;  /* 细→粗 */

/* 字体风格（斜体） */
font-style: normal;
font-style: italic;    /* 斜体 */
font-style: oblique;   /* 强制倾斜 */
```
<div style="color:pink">文字外观与颜色</div>

```
color: #333;
color: rgb(51,51,51);
color: rgba(0,0,0,0.5);

/* 行高（行间距） */
line-height: 1.5;
line-height: 24px;
line-height: 150%;

/* 字体变体（小型大写） */
font-variant: normal;
font-variant: small-caps;

```

<div style="color:pink">文字对齐与换行</div>

```
/* 水平对齐 */
text-align: left;
text-align: center;
text-align: right;
text-align: justify;  /* 两端对齐 */

/* 垂直对齐 */
vertical-align: top;
vertical-align: middle;
vertical-align: bottom;
vertical-align: baseline;

/* 自动换行 */
white-space: normal;   /* 正常换行 */
white-space: nowrap;   /* 不换行 */
white-space: pre;      /* 保留空格换行 */
white-space: pre-wrap; /* 保留换行自动换行 */

/* 文字溢出省略号 */
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;

```

<div style="color:pink">文字装饰（下划线、删除线）</div>

```
/* 文字装饰线 */
text-decoration: none;               /* 无 */
text-decoration: underline;          /* 下划线 */
text-decoration: line-through;       /* 删除线 */
text-decoration: overline;           /* 上划线 */

/* 完整写法 */
text-decoration: underline dotted red 2px;

```

<div style="color:pink">文字间距</div>

```
text-indent: 2em; /* 自动开头空 2 个汉字宽度 */
/* 字间距（字母/汉字间距） */
letter-spacing: 1px;
letter-spacing: 0.1em;

/* 词间距（英文单词） */
word-spacing: 4px;

``` 

<div style="color:pink">文字阴影</div>

```
text-shadow: 水平偏移 垂直偏移 模糊 颜色;
text-shadow: 1px 1px 2px rgba(0,0,0,0.3);
text-shadow: 0 0 5px #ff0000; /* 发光效果 */

```

<div style="color:pink">高级排版样式</div>

```
/* 大小写转换 */
text-transform: none;
text-transform: uppercase;   /* 全大写 */
text-transform: lowercase;   /* 全小写 */
text-transform: capitalize;  /* 首字母大写 */

/* 首行缩进 */
text-indent: 2em;

/* 字符方向 */
direction: ltr; /* 左到右 */
direction: rtl; /* 右到左 */

```

<div style="color:pink">CSS3 高级字体属性</div>

```
/* 字体拉伸（宽窄） */
font-stretch: normal;
font-stretch: condensed; /* 窄体 */
font-stretch: expanded;  /* 宽体 */

/* 字体平滑（抗锯齿） */
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;

```
# 好看的顏色

```
# 虛假圖片,不能是background-color
白色-淡藍:background:linear-gradient(120deg, #fff 60%, #dff9fb 60%);\
```

```
#  傾斜切分容器左邊顏色，右邊透明，下面圖片覆蓋右邊,讓圖片重心靠右
圖片要設計
.xxx-container {
  width: 100%;
  height: 1000px;
  position: absolute;
  z-index: -1;

  /* 多層背景處理 */
  background: 
    /* 第一層：上方的遮罩（左邊白色，右邊透明） */
    linear-gradient(120deg, #fff 60%, transparent 60%),
    /* 第二層：下方的圖片（會從透明處露出來） */
    url('你的圖片網址.jpg');

  /* 確保圖片正確顯示的設定 */
  background-size: cover;      /* 讓圖片鋪滿容器 */
  background-position: right;  /* 讓圖片重心靠右，避免被左邊白色擋住重要部分 */
  background-repeat: no-repeat;
}
```
# 滚动条


```
关键：允许单独滚动的区域 */
.xxx{
    overflow-y: auto;    /* 核心：垂直方向超出时自动显示滚动条 */
}


.xxx::-webkit-scrollbar {
    width: 6px; /* 纵向滚动条宽度 */
    height: 6px; /* 横向滚动条高度 */
    background: #f1f1f1;
    border-radius: 10px;
}
```


```
防止链式滚动（Overscroll Behavior）： 当局部滚动到底部时，继续滚鼠标往往会带动整个网页一起滚。
.xxx{
overscroll-behavior: contain;
}
```