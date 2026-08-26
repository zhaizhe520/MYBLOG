---
title: Android结构树
date: 2026-06-03 11:26:07
tags: Android结构树
excerpt: Android结构树
categories: 
    - Android
---

```
com.example.androidvue/
├── api/ & data/          ➡️ 大厂管这叫【Data 层】（数据层）
│                            负责去服务器拿数据、读写本地数据库。
│
├── store/ (ViewModel)    ➡️ 大厂管这叫【Domain/Presenter 层】（业务逻辑层）
│                            Pinia 在这里负责把拿到的原始数据洗干净，变成界面能用的状态。
│
└── ui/ (pages & components) ➡️ 大厂管这叫【UI 层】（视图层）
                             只负责把 ViewModel 里的数据显示出来，纯粹的工具人。
```

    