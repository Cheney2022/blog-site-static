---
title: 记一次butterfly 页脚 页头 的美化
tags:
  - butterfly
  - hexo
categories:
  - hexo
  - butterfly
keywords: "hexo,butterfly,美化"
description: 记一次butterfly 页脚 页头 的美化
# cover
abbrlink: 54540
date: 2023-09-19 15:12:23
---
#  butterfly页脚页头的美化
## 1. 修改页头
 1. 在sources 下创建assets/css/head.styl 文件 添加以下代码
 ```css
 @import 'nib'

// 顶部图
#page-header
  &, &:before
    background: transparent !important
  &.post-bg, &.not-home-page
    height: 280px !important
  #post-info
    bottom: 40px !important
  #page-site-info
    top: 140px !important

  @media screen and (max-width: 768px)
    &.not-home-page
      height: 200px !important
    #post-info
      bottom: 10px !important
    #page-site-info
      top: 100px !important

.top-img
  height: 250px
  margin: -50px -40px 50px
  border-top-left-radius: inherit
  border-top-right-radius: inherit
  background-position: center center
  background-size: cover
  transition: all 0.3s

  @media screen and (max-width: 768px)
    height: 230px
    margin: -36px -14px 36px

  [data-theme='dark'] &
    filter: brightness(0.5)

 ```
## 2. 修改页脚文件
~~在sources 下创建assets/css/footer.styl 文件 添加以下代码~~

```css
/*  页脚 */
/* 
  #footer:before
  background-color: alpha(#FFF, .1)

  [data-theme='dark'] &
    background-color: alpha(#000, .1) 
*/
``` 

$nsb:一开始想取消页脚的蓝色背景，使其和背景颜色保持统一，从作者博客以及其他博客那里了解的方案都是动用css 文件，也就是下面的方案。 最近查看的butterfly的源码，发现其本身针对页脚进行了处理， 在 主题配置文件中存在 {% label #footer_bg:  red %} 参数， 我们只需将其值改为 {% label transparent  red %} 之后 我们会发现 页脚的蓝色已经不见了，取而代之的是一层黑色遮罩。 这是因为butterfly 是默认增加黑色半透遮罩， 其控制参数为 
```yaml
 mask:  
  header: true  
  footer: true 
```
只需将其改为 {% label false  red %}  即可
```yaml
 mask:  
  header: false  
  footer: false 
```



## 3. 引入文件
 在主题配置文件中引入以上两个文件
 ```yaml
 inject:
  head: 
  - <link rel="stylesheet" href="/assets/css/head.css">
  bottom:
  - <link rel="stylesheet" href="/assets/css/footer.css">
index_img: transparent
foot_bg: transparent
 ```

