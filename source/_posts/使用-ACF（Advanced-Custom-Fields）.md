---
title: 使用 ACF（Advanced Custom Fields）
date: 2026-05-16 11:31:00
tags: ACF
excerpt: ACF
categories: 
    - WordPress
---
使用 ACF (Advanced Custom Fields) 是 WordPress 无头（Headless）开发中最核心的技能。它的本质就是在 WordPress 后台给你的文章强行增加自定义的输入框（比如专门传 Galgame 立绘、背景图、声优名字的表单），并且这些框输入的内容会直接变成合法的 JSON 数据，通过 REST API 暴露给你的 Vue 3 前端。

整个配置流程分为 WordPress 后台配置 和 Vue 3 前端对接 两部分。

# 第一部分：WordPress 后台配置（3步）
1.安装两个核心插件
在 WordPress 后台的“插件”->“安装插件”里，搜索并安装以下两个插件：

Advanced Custom Fields (ACF)：用来创建自定义字段。

ACF to REST API：（关键）这个插件会自动把你创建的 ACF 字段塞进 WordPress 默认的 /wp-json/wp/v2/posts 接口里。如果不装它，默认接口里是找不到你定义的字段的。

2.创建字段组 (Field Group)
点击后台左侧新出现的 ACF -> Field Groups -> Add New。

给这个组起个名字，比如叫 Galgame 媒体配置。

点击 + Add Field 添加新字段：

Field Label (标签名)：写中文，比如 轮播图片（给后台发文章的人看的）。

Field Name (字段名)：必须写英文/拼音，比如 gal_swiper_img（这个非常重要，它是前端代码里要点出来的变量名）。

Field Type (字段类型)：选择 Image。

Return Value (返回值)：必须选择 Image URL（这样 API 直接返回图片网址，如果是 Image Object 会返回一堆复杂的图片垃圾信息）。

在下方的 Settings -> Location Rules (显示规则) 配置：

设置为 Post Type (文章类型) is equal to (等于) post (文章)。

如果你只想让它在分类 10 里面显示，可以设置为 Post Category is equal to 你的分类名字。

3.去文章里传图
打开你分类 10 下的文章编辑器。滑到文章最底部，你会发现多出了一个叫 Galgame 媒体配置 的区域，里面有一个 轮播图片 的上传按钮。

把原本插在文章内容里的图片，重新在这里上传一次。

点击保存/更新文章。

# 第二部分：Vue 3 前端接收（极其简单）
当你在后台用 ACF 传了图，并且装了 ACF to REST API 插件后，你通过 Axios 拿到的 post 对象里，会自动多出一个叫 acf 的独立对象。

你原先抓数据的 Pinia Store 一个字都不用改，只需要改 SwiperOne.vue 组件里的 template。

1.彻底干掉正则表达式
现在你不需要再去内容里捞图了，直接点出你刚才在 ACF 里定义的 Field Name（比如 gal_swiper_img）。

2.组件模板修改：

```
把 <SwiperSlide> 里的 <img> 标签改成这样：

HTML
<template>
  <Swiper v-if="!galStore.isLoading && galStore.GalImgData.length > 0">
    
    <SwiperSlide v-for="post in galStore.GalImgData" :key="post.id">
      <img :src="post.acf?.gal_swiper_img" style="width: 100%; height: auto;" />
    </SwiperSlide>

  </Swiper>
  
  <div v-else-if="galStore.isLoading">加载中...</div>
</template>
```

# 💡 这样做为什么规范？
结构分离：文章内容（post.content.rendered）现在可以纯粹放你的文字介绍。而轮播图、立绘归属于独立的 post.acf。

绝对安全：因为 post.acf.gal_swiper_img 吐出来的就是一个纯净的字符串网址（如 http://.../uploads/xxx.jpg），前端直接赋值给 :src，再也不用担心任何 HTML 标签或者空格干扰，响应式渲染稳如磐石。