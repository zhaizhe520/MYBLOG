---
title: DOM是什麼？
date: 2026-04-15 20:00:17
tags:
categories:
  - 為什麼
---
# 前端 DOM 完全详解（新手也能看懂）

作为前端开发的核心基础，DOM 是 JavaScript 操作页面的“桥梁”——没有 DOM，网页只能是静态的文字图片，无法实现任何交互。

## 一、什么是 DOM？

**DOM（Document Object Model，文档对象模型）**，本质是浏览器提供的一套「接口规范」。它的核心作用，是把我们写的 HTML 文档，抽象成一棵「对象树」，让 JavaScript 能够读取、修改、删除、创建页面上的所有元素。

用通俗的话讲：

- HTML 是页面的「结构文本」（比如我们写的 `<div>、<p>` 标签）；

- DOM 是浏览器把 HTML “翻译”成的「可操作对象」（相当于把静态的标签，变成了 JS 能调动的“零件”）；

- JS 通过操作 DOM，实现网页的动态效果（比如点击按钮变文字、输入内容实时反馈）。

一句话总结：**DOM 是 HTML 和 JavaScript 之间的“翻译官”**，没有它，JS 就无法触碰页面的任何元素。

## 二、DOM 树结构（核心概念）

浏览器加载 HTML 后，会把页面上的所有内容（标签、文本、属性、注释）都转换成「节点（Node）」，再按照 HTML 的层级关系，组织成一棵“树”，这就是 DOM 树。

### 1. 常见节点类型（重点记3种）

- **Document 节点**：整个文档的根节点，全局可通过 `document` 访问（相当于树的“树根”）；

- **Element 节点**：HTML 标签本身（比如 `<div>、<p>、<img>）`，是我们操作最多的节点；

- **Text 节点**：元素内的文字内容（比如 `<p>Hello DOM</p>` 里的 “Hello DOM”）。

补充：还有 Attribute 节点（元素的属性，比如 id、class）、Comment 节点（HTML 注释），日常开发中用到较少。

### 2. DOM 树示例

先看一段简单的 HTML 代码：

```html
<div id="box">
  <p>Hello DOM</p>
</div>
```

浏览器解析后，对应的 DOM 树结构如下（清晰展示层级关系）：

```plain text
document（根节点）
 └── html（根标签）
      └── body（body标签）
           └── div#box（div元素，id为box）
                └── p（p元素）
                     └── textNode: "Hello DOM"（文本节点）
```

## 三、DOM 核心操作（必学，日常开发高频）

DOM 操作的核心，分为 4 大类：获取元素、修改元素、增删节点、事件绑定。逐一拆解，附详细代码示例，直接复制就能用。

### 1. 获取 DOM 元素（第一步：找到“目标”）

要操作元素，首先要找到它。以下是 4 种常用方法，重点记最后两种（现代开发首选）。

|方法|说明|代码示例|
|---|---|---|
|getElementById|通过 id 获取（唯一元素）|`document.getElementById('box')`|
|getElementsByClassName|通过 class 获取（返回集合）|`document.getElementsByClassName('item')`|
|getElementsByTagName|通过标签名获取（返回集合）|`document.getElementsByTagName('p')`|
|querySelector|CSS 选择器，获取第一个匹配元素|`document.querySelector('#box .item')`|
|querySelectorAll|CSS 选择器，获取所有匹配元素（返回集合）|`document.querySelectorAll('.list li')`|
小贴士：querySelector / querySelectorAll 支持所有 CSS 选择器（比如后代选择器、伪类选择器），灵活又强大，是现代前端开发的首选方式。

### 2. 修改 DOM 元素（最常用，实现动态效果）

找到元素后，我们可以修改它的内容、样式、属性，这是实现动态网页的核心。

#### （1）修改元素内容

两种方式，区别在于是否解析 HTML 标签：

```javascript
// 1. innerText：纯文本，不解析 HTML（推荐，更安全）
let elem = document.querySelector('#box');
elem.innerText = '新的文本内容';

// 2. innerHTML：解析 HTML 标签（慎用，有XSS风险）
elem.innerHTML = '<b>加粗的新内容</b>'; // 页面会显示加粗文字
```

#### （2）修改元素样式

通过 `style` 属性修改，注意 CSS 样式名在 JS 中要改成“驼峰命名”（比如 font-size → fontSize）：

```javascript
let elem = document.querySelector('#box');
elem.style.color = 'red'; // 文字变红
elem.style.fontSize = '20px'; // 字体大小20px
elem.style.display = 'none'; // 隐藏元素
elem.style.backgroundColor = '#f5f5f5'; // 背景色
```

#### （3）操作元素 class

当样式较多时，不推荐用 style 逐个修改，建议通过操作 class 来控制（更简洁、易维护）：

```javascript
let elem = document.querySelector('#box');
elem.classList.add('active'); // 添加 class：active
elem.classList.remove('active'); // 删除 class：active
elem.classList.toggle('active'); // 切换 class（有则删，无则加）
elem.classList.contains('active'); // 判断是否包含 class（返回 true/false）
```

#### （4）修改元素属性

针对元素的固有属性（比如 img 的 src、a 的 href、input 的 value），可直接修改，也可通过 setAttribute/getAttribute 操作：

```javascript
// 直接修改固有属性
let img = document.querySelector('img');
img.src = 'new-img.jpg'; // 修改图片地址
img.alt = '加载失败'; // 修改替代文本

let input = document.querySelector('input');
input.value = '请输入内容'; // 修改输入框内容

// 通过 setAttribute/getAttribute 操作（适合自定义属性）
elem.setAttribute('data-id', '123'); // 设置自定义属性 data-id
elem.getAttribute('data-id'); // 获取自定义属性值（返回 '123'）
```

### 3. 增删 DOM 节点（动态添加/删除元素）

有时候我们需要动态添加元素（比如点击按钮新增列表项），或删除元素（比如删除评论），这就需要用到节点的增删操作。

#### （1）创建节点

用 `createElement` 创建一个新的元素节点：

```javascript
// 创建一个 div 元素
let newDiv = document.createElement('div');
newDiv.innerText = '我是新增的元素';
newDiv.classList.add('new-item'); // 给新增元素加 class
```

#### （2）添加节点到页面

创建好的节点不会自动显示，需要添加到现有 DOM 树中（需要指定父元素）：

```javascript
let parent = document.querySelector('#parent'); // 父元素
let newDiv = document.createElement('div');
newDiv.innerText = '我是新增的元素';

// 1. appendChild：追加到父元素末尾
parent.appendChild(newDiv);

// 2. insertBefore：插入到某个参考元素前面（参考元素必须是父元素的子元素）
let referenceElem = document.querySelector('#reference');
parent.insertBefore(newDiv, referenceElem);
```

#### （3）删除节点

两种方式，推荐第二种（更简洁）：

```javascript
let elem = document.querySelector('#box');
let parent = elem.parentElement; // 获取父元素

// 1. 父元素删除子元素
parent.removeChild(elem);

// 2. 元素自身删除（现代浏览器支持，更简单）
elem.remove();
```

#### （4）克隆节点

有时候需要复制一个现有元素，用 `cloneNode`：

```javascript
let elem = document.querySelector('#box');
// 参数 true：深克隆（复制元素本身 + 所有子元素）
// 参数 false：浅克隆（只复制元素本身，不复制子元素）
let cloneElem = elem.cloneNode(true);
parent.appendChild(cloneElem); // 把克隆的元素添加到页面
```

### 4. DOM 事件（交互核心，让页面“活”起来）

DOM 最强大的功能，就是能监听用户的行为（比如点击、输入、滚动），并触发对应的 JS 代码，实现交互效果。

#### （1）绑定事件（最常用 addEventListener）

```javascript
let btn = document.querySelector('#btn');
// 绑定点击事件：第一个参数是事件类型，第二个参数是事件触发后执行的函数
btn.addEventListener('click', function() {
  alert('按钮被点击了！');
  // 这里可以写其他操作，比如修改元素内容、样式等
});
```

#### （2）常用事件类型（记熟这些，满足80%开发需求）

- `click`：鼠标点击（最常用，比如按钮、链接）；

- `input`：输入框输入（实时监听输入内容）；

- `change`：内容改变（比如下拉框选择、复选框勾选）；

- `mouseover` / `mouseout`：鼠标移入/移出元素；

- `keydown` / `keyup`：键盘按下/松开；

- `scroll`：页面或元素滚动；

- `load`：页面加载完成（避免 DOM 未加载就操作元素）。

#### （3）事件冒泡与捕获（进阶知识点）

当一个元素触发事件后，事件会沿着 DOM 树向上传播（比如点击子元素，父元素、祖父元素的同名事件也会被触发），这就是「事件冒泡」。

如果不想让事件冒泡，可以用 `e.stopPropagation()` 阻止：

```javascript
btn.addEventListener('click', function(e) {
  e.stopPropagation(); // 阻止事件冒泡
  alert('按钮被点击了！');
});
```

## 四、DOM 节点关系（遍历节点）

有时候我们需要根据一个元素，找到它的父元素、子元素、兄弟元素，这就需要用到节点关系属性：

```javascript
let elem = document.querySelector('#box');
elem.parentElement; // 获取父元素（返回 Element 节点）
elem.children; // 获取所有子元素（返回集合，只包含 Element 节点）
elem.firstElementChild; // 获取第一个子元素
elem.lastElementChild; // 获取最后一个子元素
elem.nextElementSibling; // 获取下一个兄弟元素
elem.previousElementSibling; // 获取上一个兄弟元素
```

注意：区分 Element 节点和 Node 节点（比如 `parentNode` 会返回所有类型的父节点，包括文本节点、注释节点，日常开发用 `parentElement` 更精准）。

## 五、DOM 操作的性能问题（重点避坑）

很多新手容易忽略一个问题：**频繁操作 DOM 会严重影响页面性能**。因为每次修改 DOM，浏览器都会触发「重绘（Repaint）」或「重排（Reflow）」——重排是重新计算元素的位置和大小，重绘是重新渲染元素的样式，这两个操作都很耗时。

### 常见优化方法（4个实用技巧）

1. **缓存 DOM 对象**：避免频繁查询同一个 DOM 元素，查询一次后存入变量，后续直接使用。
        `// 不好的写法（频繁查询）
for(let i=0; i<10; i++) {
  document.querySelector('#box').innerText = i;
}

// 好的写法（缓存 DOM）
let box = document.querySelector('#box');
for(let i=0; i<10; i++) {
  box.innerText = i;
}`

2. **批量修改 DOM**：先在“离线”环境（比如文档片段）中修改，再一次性插入页面，减少重排/重绘次数。

3. **使用 DocumentFragment**：文档片段是一个“虚拟容器”，可以临时存放多个节点，插入页面时只会触发一次重排。
        `let frag = document.createDocumentFragment();
// 循环创建 100 个 li 元素，先存入文档片段
for(let i=0; i<100; i++){
  let li = document.createElement('li');
  li.innerText = `列表项 ${i+1}`;
  frag.appendChild(li);
}
// 一次性插入页面，只触发一次重排
let ul = document.querySelector('ul');
ul.appendChild(frag);`

4. **避免循环中操作样式**：尽量通过修改 class 来控制样式，而非在循环中逐个修改 style 属性。

## 六、拓展：虚拟 DOM 是什么？

既然原生 DOM 操作效率低，为什么 React、Vue 等框架能实现高效的页面更新？核心就是「虚拟 DOM（Virtual DOM）」。

虚拟 DOM 的核心思想：

1. 用 JavaScript 对象，模拟 DOM 树的结构（比如用 { tag: 'div', children: [] } 模拟 <div> 元素）；

2. 当页面需要更新时，先在虚拟 DOM 上修改，对比修改前后的差异（这个过程叫「diff 算法」）；

3. 最后，只把差异部分更新到真实 DOM 上，减少真实 DOM 的操作次数。

简单说：虚拟 DOM 就是“先在草稿纸上改好，再一次性誊写到正式文档上”，极大提升了页面更新的效率。

## 七、总结（新手必看）

DOM 是前端开发的基础，也是框架的底层原理，掌握它，才能真正理解“动态网页”的本质。最后用 4 句话总结核心：

- DOM 是 HTML 的「对象化表示」，是 JS 操作页面的桥梁；

- 核心操作：获取元素 → 修改元素 → 增删节点 → 绑定事件；

- 频繁操作 DOM 会触发重排/重绘，记得用缓存、文档片段等方式优化；

- 虚拟 DOM 是对原生 DOM 的优化，框架通过它提升页面性能。

