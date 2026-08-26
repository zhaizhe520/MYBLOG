---
title: Navigation路由
date: 2026-06-07 19:54:43
tags: 路由
excerpt: navigation
categories: 
    - Android
---

```
@Composable
fun HomeScreen(navController: NavHostController) {
    Column {
        Text("这是首页")
        
        // 当点击按钮时，让管家带你去详情页
        Button(onClick = { 
            navController.navigate("detail_screen") // 下达跳转指令
        }) {
            Text("去详情页")
        }
    }
}
```

NavHostController:类型 路由类型？

```
@Composable
fun DetailScreen(navController: NavHostController) {
    Column {
        Text("这是详情页")
        
        Button(onClick = { 
            navController.popBackStack() // 下达返回指令
        }) {
            Text("返回上一页")
        }
    }
}
```

navController.popBackStack() 返回？


基本router配置

@Composable
fun xxxx(navController: NavController，modifier: Modifier=Modifier){
    NavHose(
        navController = navController,
        startDestination = "home",//开机进入的页面
        modifier = modifier

    ){

        composable(router="xx"){
            页面（）
        }

        composable(router="xx"){
            页面（）
        }

        composable(router="xx"){
            页面（）
        }

    }

}

# Object / Class 序列化路由

@Serializable  // 👈 加上这个标记，这个类就拥有了“72变”的能力


## 

se什么什么玩意

string

## 这路由简直逆天

路由断层：它卡在全局路由和子组件中间，把传递路由的“链条”给偷偷切断了！

没有把全局的 navController 传给它

rememberNavController()。这个假控制器实现虚假路由传递