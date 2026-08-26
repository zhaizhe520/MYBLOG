---
title: vue組件排版
date: 2026-04-25 18:54:55
tags:
sticky: 9
excerpt: 組件基本排版
categories:
  - Vue框架
---
# Vue3組件基本排版
```

src/
├─ composables/       ✅ 组合式函数（自定义 Hooks）
├─ components/        ✅ 所有小组件
│   ├─ layout/        ✅ 布局：导航、底部、侧边栏
│   ├─ common/        ✅ 通用：按钮、卡片、列表、输入框
│   ├─ home/          ✅ 首页专用组件
│   └─ user/          ✅ 会员专用组件
├─ views/             ✅ 所有页面（路由页面）
│   ├─ Home.vue
│   ├─ Works.vue      ✅ 作品列表页
│   ├─ About.vue
│   └─ user/
│       ├─ Login.vue
│       └─ Info.vue
├─ stores/            ✅ Pinia 全局数据仓库
│   └─ useWorkStore.js ✅ 存放所有作品数据、搜索数据
├─ router/            ✅ 路由
├─ assets/            ✅ 图片、CSS、字体、静态资源
├─ utils/             ✅ 工具函数
├─ api/               ✅ 接口请求
├─ styles/            ✅ 全局 CSS、主题、变量
├─ constants/         ✅ 常量、枚举、固定数据
└─ main.js            ✅ 入口文件
 ```
 # Vue3 选项式 API

在 Vue 3 中，選項式 API (Options API) 是一種撰寫組件的模式。之所以稱為「選項式」，是因為我們是透過一個包含多個「選項」（例如 data、methods、mounted）的物件來描述組件的邏輯。

 ```
 export default {
  // 组件名
  name: "Demo",

  // 子组件注册
  components: {},

  // 父向子传参
  props: {
    title: {
      type: String,
      default: ""
    }
  },

  // 数据、变量、开关
  data() {
    return {
      msg: "hello vue3",
      flag: false,
      list: []
    };
  },

  // 计算属性
  computed: {},

  // 监听
  watch: {},

  // 方法、点击事件、函数
  methods: {
    handleClick() {
      
    }
  },

  
  created() {},

  // 生命周期
  mounted() {
    console.log('組件已掛載！');
  },
  
  unmounted() {}
};
```