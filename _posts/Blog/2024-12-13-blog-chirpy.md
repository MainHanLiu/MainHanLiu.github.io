---
title: "博客搭建 — chirpy"
description: 
date: 2024-12-13 10:00:00 +0800
categories: [Blog, chirpy]
tags: [Blog, chirpy]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text under the image
---

## Jekyll
- 选择大于努力  
在ubuntu18版本尝试了许多方法装不上最新的ruby，新装了一个22版本后，很快就环境就成功安装了，但是在初始化博客环境时仍然有报错，无法完全成功运行初始化脚本，因此再次选择基于最新的ubuntu24进行搭建，最终比较顺畅地完成了环境搭建和成功运行初始化脚本。

## Chirpy

### 网站图标替换
Comment By sherrywang31 in [# Customize the Favicon](https://chirpy.cotes.page/posts/customize-the-favicon/) :  
>it seems that it's due to the favicon generator listed in the instruction is now missing some png files, and so the default favicon are used in those situations. I used [this generator](https://favicon.io/favicon-converter/) in addition to the generator listed above to generate favicon .png files, and moved all resulting files from generator listed in instruction (excluding the 2 mentioned in instruction) together with `favicon-16x16.png`, `favicon-32x32.png`, `android-chrome-192x192.png`, `android-chrome-512x512.png` files from generator I mentioned into the `assets/img/favicons/` and it works. Example: [https://github.com/sherrywang31/sherrywang31.github.io](https://github.com/sherrywang31/sherrywang31.github.io)

使用以下两个生成器并替换相应文件（经测试验证，网站标题头像使用的是16x16）  
[# Favicon Generator for perfect icons on all browsers](https://realfavicongenerator.net/)  
[# Favicon Generator - Image to Favicon - favicon.io](https://favicon.io/favicon-converter/)  

###  一些功能的官方网站

- 评论系统 giscus  
[giscus](https://giscus.app/zh-CN)

- 站点统计  
[不蒜子 | 不如](https://ibruce.info/2015/04/04/busuanzi/)

- 谷歌跟踪  
[google analytics](https://analytics.google.com/analytics/web/)

### Chirpy 优秀博客  
- 官方文档  
[Chirpy](https://chirpy.cotes.page/)

- 某热心人士的官方文档汉化版  
[Chirpy-CN](https://pansong291.github.io/chirpy-demo-zhCN/)

- 搭建教程、搜索优化、进阶技巧  
[Jekyll | N・H・K にようこそ！](https://ittousei.github.io/categories/jekyll/)

- 侧边栏头像框修改、右下角添加sticker  
[对Chirpy进行简单美化 | manalogues](https://manalogues.com/posts/%E5%AF%B9Chirpy%E8%BF%9B%E8%A1%8C%E7%AE%80%E5%8D%95%E7%BE%8E%E5%8C%96)

- 图片粘贴工具  
[搭建个人博客：Jekyll + Github Pages + VSCode | zjpzhao's Blog](https://zjpzhao.github.io/posts/jekyll-githubpages/#%E7%B2%98%E8%B4%B4%E5%9B%BE%E7%89%87%E5%B7%A5%E5%85%B7-vscode%E6%8F%92%E4%BB%B6paste-image)

- 自定义首页  
[giannischen](https://giannischen.github.io/categories/blog-tutorial/)

- ***侧边栏背景、随机动画、站点统计、LQIP***  
[Chirpy Blog Customization | Huanyu Shi](https://huanyushi.github.io/posts/chirpy-blog-customization/)

- 站点跟踪、搜索收录、图片CDN、自定义首页  
[Build | Marvin's blog](https://winxuan.github.io/categories/build/)

### 待办
- 搜索引擎收录   
[Jekyll站点添加至谷歌收录 | 青梅煮酒](https://wilson1202.github.io/posts/add-jekyll-site-to-google-index/)  
[Google搜索收录Github Pages 自动化(Blog可以被Google搜索到) | Marvin's blog](https://winxuan.github.io/posts/blog-google-search-include-auto/)  

## else theme
以前看过的另一个博客主题搭建教程：
[qiubaiying 博客搭建详细教程](https://github.com/qiubaiying/qiubaiying.github.io/wiki/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E8%AF%A6%E7%BB%86%E6%95%99%E7%A8%8B#Rename)
