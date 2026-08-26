---
title: Jetpack Compose（声明式 UI 框架）
date: 2026-06-03 10:24:43
tags: Jetpack Compose（声明式 UI 框架）
excerpt: Jetpack Compose（声明式 UI 框架）
categories:
    - Android
---

<div style="color:pink">声名式UI架构</div>


# @Composable 这个组件。

在 Android 里，凡是加了 @Composable 的函数，我们称之为 “可组合函数”。

Vue 里的 Component（组件）！


Greeting 组件：

```
@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Text(
        text = "你好！",
        modifier = Modifier
    )
}
```

name: String：这不就是 Vue 里的 props 吗！父组件传什么进来，这里就接收什么。

Text(...)：这是一个内置的文字组件，专门用来渲染文本。

Modifier（修饰符）：这玩意相当于前端的 CSS / Style / Class。用来控制这个组件的宽高、边距
（padding）、背景色等等。


# 拆解 MainActivity（整个 App 的大外壳）

```
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge() // 开启沉浸式状态栏（让界面顶到手机最上方，不被黑边挡住）
        
        setContent { // 👈 相当于 Vue 的 app.mount('#app')！把 UI 挂载到手机屏幕上
            AndroidVueTheme { // 相当于全局主题配置（Provider）
                Scaffold( modifier = Modifier.fillMaxSize() ) { innerPadding -> // 页面脚手架，帮你留出状态栏和底部导航栏的空隙
                    Greeting(
                        name = "Android",
                        modifier = Modifier.padding(innerPadding) // 把空隙当做 padding 传给子组件
                    )
                }
            }
        }
    }
}
```

<div style="color:pink">Jetpack Compose 走的是 “万物皆函数” 的路线：</div>


有点麻
```
modifier(声明参数名) = Modifier(基础空对象)新的子
                   .

```




# 布局 

 dp 是会根据手机屏幕密度自动缩放的“智能单位”。

实际的渲染=dp*(手机密度除以160)




## Box 的潜规则

Box 的本质不是普通的 `<div>`，它相当于前端写了 position: relative; 的绝对定位容器。

专门用来做网页里需要“绝对定位”或者“层叠”的场景。

# Column（垂直排列容器）。列

# Row —— 水平排列（前端的 flex-direction: row）


# 按钮

```
Button(
    onClick = { /* 点击事件 */ },
    // 所有的【CSS样式】，全部塞进下面这个 modifier 参数里链式调用
    modifier = Modifier
        .width(100.dp)   // 相当于 width: 100px (用 dp 适配)
        .height(50.dp)  // 相当于 height: 50px
) {
    // 按钮里面的【HTML文本结构】，直接作为子元素写在括号里
    Text(text = "详情页") 
}
```
# 公式
```
@Composable
fun 页面（modifier: Modifier = Modifier）可复用{
    BOX()


}

```
horizontal（水平方向的）

Arrangement（排列方式）





端居中
```
modifier = Modifier
.fillMaxWidth()
.padding(16.dp),
horizontalArrangement = Arrangement.SpaceBetween
```

水平居中
```
modifier = Modifier
.fillMaxWidth()
.padding(16.dp),
horizontalArrangement = Arrangement.CenterHorizontally
```


# 状态向下传递（数据），事件向上传递（暴露 fun）。




简单来说，Modifier（修饰符）就是一个普通的 Java/Kotlin 对象，它的作用是告诉系统如何更改、装饰或对某个 UI 元素（Composable）的行为做出反应。

modifier: Modifier = Modifier


小写的modifier是外面给的权利

大写的Modifier是内部给的权利


padding() 写在背景/大小的前面 —— 它就是“外边距”

当 padding() 写在背景/大小的后面 —— 它就是“内边距”

.size(40.dp)正方形

.size(width = 数字.dp, height = 数字.dp)

撑满全屏：.fillMaxSize()

宽度撑满，高度随缘：.fillMaxWidth()

Modifier 负责的是“通用外在属性”： 比如把你放大（size）、把你移位（padding）、给你上个底色（background）、让你能被手指点（clickable）。

这些是所有 UI 组件共通的。

声明式 UI 框架里非常经典的“属性 + 事件Listener” 的通用写法。

大家更喜欢直接调用系统主题色（MaterialTheme），它会自动适配手机的深色模式（夜间模式）和浅色模式：

`color = MaterialTheme.colorScheme.onBackground`

 `contentAlignment = Alignment.Center`居中


 # Flutter Box 九宫格 Alignment 对照表(对齐方式)

| 对齐枚举参数 | 通俗位置说明 | 九宫格方位 |
| ---- | ---- | ---- |
| Alignment.TopStart | 左上角 | 第一行第一列 |
| Alignment.TopCenter | 顶部水平居中 | 第一行第二列 |
| Alignment.TopEnd | 右上角 | 第一行第三列 |
| Alignment.CenterStart | 左侧垂直居中 | 第二行第一列 |
| Alignment.Center | 容器正中心（水平+垂直双居中） | 第二行第二列 |
| Alignment.CenterEnd | 右侧垂直居中 | 第二行第三列 |
| Alignment.BottomStart | 左下角 | 第三行第一列 |
| Alignment.BottomCenter | 底部水平居中 | 第三行第二列 |
| Alignment.BottomEnd | 右下角 | 第三行第三列 |

## 补充说明
1. **Start / End 受文字方向影响**
默认从左到右布局（中文/英文）：`Start=左、End=右`；阿拉伯文等从右向左排版时，`Start=右、End=左`。
2. **使用场景**
`Container`、`Align`组件的`alignment`属性直接传入该枚举，控制子组件在父容器内的摆放位置：


```
Container(
  alignment: Alignment.Center, // 子控件居中
  child: Text("测试"),
)
```

Arrangement（排列） vs Alignment（对齐）


horizontalArrangement（水平排列）。

verticalAlignment（垂直对齐）。

Arrangement.spacedBy(xx.dp)彼此留xx间距


排列方式

对齐方式

#  图片背景
```
Box(modifier = modifier.fillMaxSize()) {
        Image(
            painter = painterResource(id = R.drawable.arora),
            contentDescription = "背景图",
            contentScale = ContentScale.Crop,
            alpha = 0.2f,
            modifier = Modifier.fillMaxSize()
        )
        xxxx(
            modifier = Modifier,
       
        ) {
            xxx

        }
    }
```