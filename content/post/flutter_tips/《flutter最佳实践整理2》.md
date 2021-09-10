---
title: "《Flutter最佳实践整理2》"
subtitle: ""
date: 2021-07-30T22:36:13+08:00
lastmod: 2021-07-30T22:36:13+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍

<!--more-->

## 文本划线

通过Text的decoration属性来实现划线

- TextDecoration.none 没有
- TextDecoration.underline 下划线
- TextDecoration.overline 上划线
- TextDecoration.lineThrough 中间的线(删除线)

// 划线相关的属性设置
decorationColor decoration划线的颜色
decorationStyle decoration划线的样式

- TextDecorationStyle.solid 实线
- TextDecorationStyle.double 画两条线
- TextDecorationStyle.dotted 点线(一个点一个点的)
- TextDecorationStyle.dashed 虚线(一个长方形一个长方形的线)
- TextDecorationStyle.wavy 正玄曲

效果图如下

![best_practise_01](https://luckly007.oss-cn-beijing.aliyuncs.com/img/best_practise_01.png)