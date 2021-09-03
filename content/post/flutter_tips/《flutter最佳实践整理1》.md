---
title: "《Flutter最佳实践整理1》"
subtitle: ""
date: 2021-07-30T22:15:36+08:00
lastmod: 2021-07-30T22:15:36+08:00
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



> 本文主要介绍Text 以字符的方式截断

<!--more-->

## Text 以字符的方式截断

https://github.com/flutter/flutter/issues/52869

在flutter中，Text控件默认的溢出显示模式是`TextOverflow.fade` ,就是淡出

在iOS或者Android平台默认的文件截断模式一般是…省略，flutter里面对应的截断模式为`TextOverflow.ellipsis` ,不过这里的截断是英文按照单词来的，这样的模式会导致如果最后一个单词很长时，截断显示会整理省略而不是最后超出的字符省略，导致模块可能有一大块空白。

Dart系统定义的截断模式

```
/// How overflowing text should be handled.
///
/// A [TextOverflow] can be passed to [Text] and [RichText] via their
/// [Text.overflow] and [RichText.overflow] properties respectively.
enum TextOverflow {
  /// Clip the overflowing text to fix its container.
  clip,

  /// Fade the overflowing text to transparent.
  fade,

  /// Use an ellipsis to indicate that the text has overflowed.
  ellipsis,

  /// Render overflowing text outside of its container.
  visible,
}
```

如何解决英文单词被整体截断呢？

将单词的每个字符切割开，插入宽度0的占位字符，打破系统默认的机制，这样就可以以字符为单位省略了

> 需要注意。这种方式相当于修改了文本的内容，一般文本最大一行显示可以用，如果文本支持2行以及以上的显示的话，将会导致换行不再按照字符进行而按照单词进行

下面是Example实现

```
extension TextOverflowUtil on String {
  /// 将flutter系统默认的单词截断模式转换成字符截断模式
  /// 通过向文本中插入宽度为0的空格实现
  static String toCharacterBreakStr(String word) {
    if (word == null || word.isEmpty) {
      return word;
    }
    String breakWord = '';
    word.runes.forEach((element) {
      breakWord += String.fromCharCode(element);
      breakWord += '\u200B';
    });
    return breakWord;
  }
}
```

