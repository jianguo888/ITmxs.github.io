---
title: "《Flutter中的提示工具》"
subtitle: ""
date: 2021-08-20T10:47:09+08:00
lastmod: 2021-08-20T10:47:09+08:00
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

当用户将鼠标悬停、点击或聚焦在某个元素上时，工具提示会显示一条信息性消息。

在 Flutter 中，您可以使用名为 Tooltip 的内置小部件轻松创建工具提示。本文将带您了解 Tooltip 小部件的基础知识以及在实践中使用它的示例。

为了向小部件添加工具提示，您需要做的是将该小部件包装在一个工具提示小部件中，如下所示：

```dart
Tooltip(
   message: 'This is a tooltip',
   child: OutlinedButton(onPressed: () {}, child: const Text('A Button')),
)
```

常用选项：

- **height** : 指定工具提示的高度
- **padding** : 工具提示的填充
- **margin** : 工具提示的边距
- **装饰**：控制工具提示的形状和背景颜色
- **preferBelow**：工具提示是否会显示在孩子的下方
- **showDuration** : 工具提示将显示多长时间
- **textStyle** : 工具提示消息的样式
- **waitDuration**：在消息出现之前指针必须在工具提示上的时间量
- **verticalOffset** : 小部件和显示的工具提示之间的垂直间隙
- **enableFeedback**：工具提示是否提供声音和/或触觉反馈



```dart
Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('KindaCode.com'),
      ),
      body: Center(
          child: Tooltip(
              message: 'This is a cute dog. Do you like him?',
              padding: const EdgeInsets.all(30),
              margin: const EdgeInsets.only(top: 80, left: 50),
              decoration: BoxDecoration(
                  color: Colors.indigo,
                  borderRadius: BorderRadius.circular(20)),
              textStyle: const TextStyle(
                  fontSize: 15,
                  fontStyle: FontStyle.italic,
                  color: Colors.white),
              child: SizedBox(
                width: 320,
                height: 240,
                child: Image.network(
                  'https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210507081032836.png',
                  fit: BoxFit.cover,
                ),
              ))),
    );
  }
```

https://github.com/shenhuaxiyuan/flutter_jd_address_selector/tree/master/lib

![](D:\study_manger\hugo_LoveIt_blog\content\post\flutter_tips\flutter提示工具.assets\image-20210507081032836.png)