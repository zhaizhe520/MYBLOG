---
title: vue3高阶函数来控制异步请求与loading动画
date: 2026-05-07 20:44:49
tags: vue3高阶函数来控制异步请求与loading动画
excerpt: loading动画
categories: 
  - Vue框架
---
这是一个非常棒的问题。你现在接触到的这种“高阶函数 + 路由守卫”的思路，其实触及到了企业级开发中非常核心的两个概念：声明式编程和中心化控制。在真正的企业级项目中，做法会有一些演变，但核心逻辑是一致的。

1. 企业里真的用高阶函数吗？是的，但形式可能更隐蔽。大厂或中大型项目的代码通常追求“低耦合”。虽然不一定每个项目都手写一个叫 withRouteLoader 的函数，但他们会通过以下方式实现相同逻辑：装饰器模式 (Decorators)： 在一些使用 TypeScript 的企业项目中，会用类似 @LoadData(fetchAction) 的装饰器直接挂在路由组件上。统一中间件 (Middleware)： 类似 Nuxt.js（Vue 的服务端渲染框架）里的 asyncData 或 middleware。

它本质上就是把异步抓取逻辑从组件内部抽离到了路由层。路由配置化： 很多企业级后台框架会把路由写成一个巨大的 JSON 配置文件，里面有一个 beforeEnter 字段，统一调用一个通用的 Loader 函数。


2. 加载动画的控制权关于加载动画（Loading），企业开发通常遵循 “谁发起请求，谁控制状态” 的原则，但为了视觉统一，会做分层：方案适用场景企业应用程度全局进度条 (nprogress)路由跳转时顶部的细线进度条。90% 的项目必备，简单直接。高阶函数控制全屏 Loading关键数据加载，不加载完页面没法看的场景（如你现在的项目）。常见于交互感强的项目，能避免页面内容闪烁。骨架屏 (Skeleton Screens)列表页、详情页。现代互联网大厂首选，用户感知的加载速度最快。

3. 为什么企业更倾向于这种“路由拦截”写法？如果你在面试时提到这种“立案”方案，面试官会觉得你考虑到了以下企业级痛点：防止“竞态条件” (Race Conditions)：如果用户在 1 秒内连续点击了 3 个路由，组件内的 onMounted 可能会发起 3 个请求，最后回来的数据可能会乱序。在高阶函数控制的路由守卫中，新导航会直接取消旧导航，天然规避了这个问题。错误边界 (Error Boundaries)：如果后端接口崩了（WordPress API 报错），高阶函数可以在进入页面前就把用户拦截在错误页，而不是让用户进入页面后看到一个满是 undefined 的破碎网页。用户体验 (UX) 的确定性：企业级产品最忌讳“不确定性”。让用户在旧页面多看半秒 Loading，也比让用户进入一个“加载了一半、错位了一半”的新页面要好。

4. 一个企业级的“立案”小细节在企业开发中，写这种逻辑通常还会加一个“最小加载时间”：JavaScript// 有时候接口太快，Loading 闪一下就没了，反而晃眼

```
// 企业级写法会保证 Loading 至少显示 300ms
await Promise.all([
  action(),
  new Promise(resolve => setTimeout(resolve, 300))
]);
```

# json文件来控制我的路由loading动画，像企业那样

这是一个非常硬核且具有“工程化”思维的想法。在企业级项目中，这种做法通常被称为“配置化路由”或“数据驱动 UI”。

你可以创建一个 JSON 文件来定义每个路由的加载行为（比如：是否显示全屏动画、加载文字是什么、超时时间是多少）。这样做的好处是：如果你想改某个页面的加载效果，直接改 JSON 配置，不需要动业务代码。

1. 设计你的配置文件 router-config.json
在 src/config/ 目录下创建这个文件：

JSON
{
  "default": {
    "showLoading": true,
    "loadingText": "正在接入服务器...",
    "timeout": 5000
  },
  "routes": {
    "/": {
      "loadingText": "正在初始化博客首页...",
      "transition": "fade"
    },
    "/post": {
      "loadingText": "正在解析 Markdown 文档...",
      "showLoading": true
    },
    "/pet-room": {
      "loadingText": "正在召唤 Live2D 宠物...",
      "timeout": 10000
    }
  }
}
2. 编写高阶函数读取配置
我们需要修改之前的 withRouteLoader，让它在执行异步任务时，先去匹配 JSON 里的配置。

JavaScript
```
import routeConfig from '@/config/router-config.json';
import { useAppStore } from '@/stores/appStore';

export function withRouteLoader(action) {
  return async (to, from, next) => {
    const appStore = useAppStore();
    
    // 1. 根据当前路径匹配配置，如果没有匹配到则使用默认配置
    const config = routeConfig.routes[to.path] || routeConfig.default;

    // 2. 如果配置要求显示 Loading
    if (config.showLoading) {
      appStore.setLoadingStatus({
        visible: true,
        text: config.loadingText
      });
    }

    try {
      // 3. 执行异步任务（设置超时，防止页面死锁）
      await Promise.race([
        action(to),
        new Promise((_, reject) => 
          setTimeout(() => reject(new Error('请求超时')), config.timeout || 5000)
        )
      ]);
      
      next();
    } catch (error) {
      console.error("【立案】加载失败:", error);
      next({ name: 'Error' }); 
    } finally {
      // 4. 关闭动画
      appStore.setLoadingStatus({ visible: false });
    }
  };
}
```
3. 在 Pinia 中管理状态
你的 appStore 也要配合这个 JSON 的结构：

JavaScript
```
export const useAppStore = defineStore('app', {
  state: () => ({
    loading: {
      visible: false,
      text: ''
    }
  }),
  actions: {
    setLoadingStatus({ visible, text = '加载中...' }) {
      this.loading.visible = visible;
      this.loading.text = text;
    }
  }
});
```
4. 为什么企业级开发喜欢这么干？
非技术人员友好：如果公司里的产品经理（PM）觉得加载文字太死板，你甚至可以把这个 JSON 放在服务器后端。PM 改一下后台，前端的加载动画文字就变了，无需重新打包发布代码。

解耦：路由跳转逻辑和 UI 展示逻辑通过 JSON 文件解耦。你的 withRouteLoader 变成了一个纯粹的“执行引擎”。

易于维护：当你项目有 50 个路由时，在一个 JSON 里查看所有页面的加载策略，比翻 50 个 .vue 文件要高效得多。

基本流程

```
用户点击连接

Vue Router 拦截 -> 触发 withRouteLoader

AppStore -> isLoading = true (展示你 JSON 配置的文字)

Axios -> 抓取 WordPress API

Pinia -> 存储抓到的 JSON

Next() -> 关闭 Loading，页面瞬间填满数据
```
...........
skeleton