---
title: 2中方法抓wp的特色圖片
date: 2026-05-03 19:32:29
tags: 2種方法抓wp特色圖片
excerpt: 抓wp特色圖片
categories: 
  - Vue框架
---
# 第一種
```
// 在 useWorkStore.js 的 fetchWpPosts Action 中
const res = await axios.get('http://110.42.248.8/wp-json/wp/v2/posts?_embed')

this.pageAllData[1] = res.data.map(post => {
  // 安全地抓取特色圖片地址
  let featuredImg = '';
  try {
    // 路徑通常是：_embedded -> wp:featuredmedia -> [0] -> source_url
    featuredImg = post._embedded['wp:featuredmedia'][0].source_url;
  } catch (e) {
    featuredImg = '默認圖片地址'; // 如果沒設置特色圖片，給個備用圖
  }

  return {
    imgSrc: featuredImg,
    textName: post.title.rendered,
    // ...其餘字段
  }
})
```
# 第二種 修改 WordPress 的 functions.php (後端方案)

```
add_action('rest_api_init', 'register_rest_images' );
function register_rest_images(){
    register_rest_field( array('post'),
        'fimg_url', // 前端看到的字段名
        array(
            'get_callback'    => 'get_rest_featured_image',
            'update_callback' => null,
            'schema'          => null,
        )
    );
}
function get_rest_featured_image( $object, $field_name, $request ) {
    if( $object['featured_media'] ){
        $img = wp_get_attachment_image_src( $object['featured_media'], 'app-thumb' );
        return $img[0]; // 返回圖片 URL
    }
    return false;
}
```