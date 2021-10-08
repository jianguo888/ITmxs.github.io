---
title: "《Flutter学习资料推荐2》"
subtitle: ""
date: 2021-04-28T18:17:07+08:00
lastmod: 2021-04-28T18:17:07+08:00
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

# 学习资料

## 网站和公众号

官网通常是最权威的学习资料来源，所以强烈推荐优先看 Flutter 官网。Flutter 基于 Dart 平台，所以了解和熟悉 Dart 是非常必要的。官方还会不定期在 [medium](https://medium.com/flutter) 发一些技术文章，介绍 Flutter 的新特性，建议也关注。

- [Dart 官网](https://dart.dev/)
- [Flutter 官网](https://flutter.dev/)
- [medium](https://medium.com/flutter)
- 谷歌开发者公众号

考虑到 Flutter 官网内容是英文，英文不好的话浏览起来会比较吃力的，也可退而求其次看中文网站：

- [Flutter 中文](https://flutter.cn/)
- [Dart 中文](https://dart.cn/)

中文内容的好处，

- 消除了语言障碍，方便快速浏览学习
- 中文网站通常在国内，打开速度可能快得多

但也要注意其不足：

- 访问可能不稳定 (通常不由官方支持，说不定哪天就没了)
- 某些内容相当不准确
  - 一些新术语很难翻译
  - 大量内容由机器翻译，不靠谱
- 内容更新不及时 (Flutter 和 Dart 都在快速更新中)

谷歌开发者公众号是 Google 中国官方账号，汇集 Android、Flutter、Chrome OS 以及 AI 等开发技术。2019 年以及随着 Flutter 的爆发，这个公众号上关于 Flutter 的内容明显增多，建议关注。

![-w187](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15736157127923.jpg)

谷歌开发者公众号上曾推出过几期 《Flutter 您需要知道的知识点》，这里整理如下：

- [Flutter 您需要知道的知识点 | FAQ・第一期](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652048481&idx=1&sn=3775bb6e61b9b4d7d7c5a48871a13807&chksm=808cac24b7fb2532858b94dc45f1ec4f8ec83f28f186e0cbc53a40034b3398feddea553e3054&mpshare=1&scene=1&srcid=1113sj4rrsidBwb6ecBCdZ86&sharer_sharetime=1573616038315&sharer_shareid=b5535657e3516bd6d7252ce5f5ed09f4&rd2werd=1#wechat_redirect)
- [Flutter 您需要知道的知识点 | FAQ・第二期](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652048715&idx=1&sn=c8346d2603f311301d719d5374cc77ea&chksm=808cad0eb7fb241864557d2bc08cacc2efeb76a27d3a6b849d2df44ccbddfa13c3e1b1db0f85&mpshare=1&scene=1&srcid=1113h6WpLLfSkAf6rNSO5Yc5&sharer_sharetime=1573616056926&sharer_shareid=b5535657e3516bd6d7252ce5f5ed09f4&rd2werd=1#wechat_redirect)
- [Flutter 您需要知道的知识点 | FAQ・第三期](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652049096&idx=1&sn=dc9d0a408d07d78d487974354fd5dee7&chksm=808cae8db7fb279b3b2d183246a6759ccbc3d1d51420c7a7a5640f89206a1a20985d8d3f1b07&mpshare=1&scene=1&srcid=1113f7jM3rxmuXGnN5NFNgzQ&sharer_sharetime=1573616077912&sharer_shareid=b5535657e3516bd6d7252ce5f5ed09f4&rd2werd=1#wechat_redirect)
- [Flutter 您需要知道的知识点 | FAQ・第四期](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652049594&idx=1&sn=26434fd1aa93efb4aff12018365607cf&chksm=808cb0ffb7fb39e9c4ccc010dd5dacd02faf326e22a70dae95783898e32b80c9102e94f9ec84&mpshare=1&scene=1&srcid=11130i26qvOXs6rDzq7uSpdx&sharer_sharetime=1573616095494&sharer_shareid=b5535657e3516bd6d7252ce5f5ed09f4&rd2werd=1#wechat_redirect)
- [Flutter 您需要知道的知识点 | FAQ・第五期](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652049986&idx=1&sn=adcb4fd598f8f37a83c98b3ae5fb478c&chksm=808cb207b7fb3b11b969337386db9295f58c41f82131ad72de927ca3da80a31d4b0422234b4a&mpshare=1&scene=1&srcid=1113cwoJv70OIw09MTgbsUFi&sharer_sharetime=1573616122448&sharer_shareid=b5535657e3516bd6d7252ce5f5ed09f4&rd2werd=1#wechat_redirect)
- [Flutter 您需要知道的知识点 | FAQ・第六期](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652050376&idx=1&sn=2034b412bb113de7b208e18c3f74508f&chksm=808cb38db7fb3a9b0c9c5d1fcb2d8a746d365c8eeb9ae5d2d4ad9f2ff1496c17c0851ecb26df&mpshare=1&scene=1&srcid=1113xZMxrEdhsqDfbyi6edBg&sharer_sharetime=1573616160182&sharer_shareid=b5535657e3516bd6d7252ce5f5ed09f4&rd2werd=1#wechat_redirect)
- [Flutter 您需要知道的知识点 | FAQ・第七期](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652050932&idx=1&sn=4284ce654f5ba9365845583cfc674bed&chksm=808cb5b1b7fb3ca7a074b16b027bd3a4c8a79f190cf7b046adcd17e028568fbe5cce59e8ce2e&mpshare=1&scene=1&srcid=1113upVjIoYooOEWY0gf8KPO&sharer_sharetime=1573616174633&sharer_shareid=b5535657e3516bd6d7252ce5f5ed09f4&rd2werd=1#wechat_redirect)

(谷歌知道大家学不过来，所以将 Flutter 重点知识做成了卡片，让大家好时不时复习巩固一下，早日写出优秀的 Flutter 应用)

另外，谷歌开发者公众号上的 Flutter 相关的内容汇总在 `开发专栏 > 重点知识巩固 > UI框架`。

## 代码

### 官方代码

> Talk is cheap, show me the code

如果你的学习习惯是多看代码，那么首推官方示例。官方示例分布在不同的仓库中：

- [官方 examples](https://github.com/flutter/flutter/tree/master/examples) - 包含若干入门的简单示例
- [官方 samples](https://github.com/flutter/samples) - Flutter 最佳实践
- [A curated list of samples](https://github.com/flutter/samples/blob/master/INDEX.md) - 嫌上面的例子还不够的话，这个列表中的项目够你看的

不要小看 [官方 examples](https://github.com/flutter/flutter/tree/master/examples)，它使用最简短的代码演示了最丰富的基础功能，包括：

- **Hello, world** 最经典的 “hello, world”，以及一个阿拉伯文版本的 “hello, world” (国际化？Flutter 能行！)
- **Image List** 流行的网络图片列表界面 (这个 demo 还顺手写了个简单的 HTTP Sever)
- **Flutter gallery** 演示了各种 Widget 的用法
- **Platform Channel** 演示了 Flutter 如何跟 Android/iOS 交互

[官方 samples](https://github.com/flutter/samples) 则演示了 Flutter 高级功能和最佳实践：

- **Animations**
- **Isolate Example**
- **Provider 状态管理**

官方例子的优势是权威、内容丰富，更新及时，是我们学习的好资料。它的不足则在于往往非常理想化、代码过于”干净”，实际应用常遇到机型兼容问题、产品需求等情况导致”恶心”的代码，例子可以参考，遇到的坑还是要自己一点点填。

### 第三方代码

> 我们的大脑理解抽象内容很吃力，我们喜欢有形的事物。当事物是直接的而且是有形的时候，就显得容易理解。
> 乌尔里希·伯泽尔《有效学习》

很多第三方代码有类似这样的功能，能边看代码边看效果，有助于快速理解 Flutter UI。

## 视频和书籍

如果你的学习习惯是看视频，推荐如下几个视频：

- [Flutter Widget of the Week](https://www.youtube.com/watch?v=lkF0TQJO0bA&list=PLjxrf2q8roU23XGwz3Km7sQZFTdB996iG&index=2) - 每个视频在一两分钟内生动形象地演示一个 Widget 的主要功能和用法，适合快速入门 Flutter Widget
- [The Boring Flutter Dev Show](https://www.youtube.com/watch?v=rfagvy5xCW0&list=PLOU2XLYxmsIK0r_D-zWcmJ1plIcDNnRkK&index=4)

个人感觉通过视频来学习的缺点是比较费时间，时不时得快进，也很难快速抓住重点，当然 [Flutter Widget of the Week](https://www.youtube.com/watch?v=lkF0TQJO0bA&list=PLjxrf2q8roU23XGwz3Km7sQZFTdB996iG&index=2) 除外 (它已经帮你划好重点)

如果你的学习习惯是看书，推荐如下几本书：

- Flutter 实战

   

  由浅入深的介绍了 Flutter 技术和开发流程。该书包含不仅包含大量示例、图片，还有配套的示例源码，可帮助读者循序渐进的掌握 Flutter 开发技术。

  - 入门篇(第1章~第7章)- 常用的组件以及布局
  - 进阶篇(第8章~第14章)- 事件机制、动画、自定义组件、文件和网络、插件、国际化以及Flutter核心原理
  - 实例篇 (第15章)- 实现一个简版的 Github APP

如果你还知道其他不错的视频或书籍资源，欢迎在评论中分享出来。

## 其他

这里是几篇不错的文章：

- [Interesting Features of the Dart Programming Language](https://renato.athaydes.com/posts/interesting-dart-features.html)
- [Awesome Dart](https://github.com/yissachar/awesome-dart)
- [Flutter原理与实践 - 美团技术团队](https://tech.meituan.com/2018/08/09/waimai-flutter-practice.html)
- [Flutter Engine 编译指北 | 区长](https://fucknmb.com/2019/02/26/Flutter-Engine-编译指北/)
- [Flutter – cc’s blog](https://www.ccarea.cn/archives/category/flutter)

# 开发工具

[DartPad](https://dartpad.dartlang.org/) 是学习 Dart 语法和尝试 Dart 语言特性的最佳方式，还可以体验 Dart 核心库(除了 dart:io 库和依赖 dart:io 的库)。

VS Code 是一个**轻量级**编辑器。我使用它没别的原因，Android Studio 强虽强，笔记本性能有限，资源能省则省！

https://dartcode.org/ dart 和 flutter 插件

Android Studio

[TODO]

# 练习与实践

## 练习

> 纸上得来终觉浅，绝知此事要…

[Dart cheatsheet codelab | Dart](https://dart.dev/codelabs/dart-cheatsheet) - 这个练习用于快速掌握 Dart 语言特性

- 默写 demo
- 实现 freecodecamp 上的练习

除了看例子，还可以跟着 Codelabs 动手

- [Dart Codelabs](https://dart.dev/codelabs)
- [Flutter Codelabs](https://flutter.dev/docs/codelabs)

## 实践

- [TODO] 集成和构建。关键问题：so 兼容问题
- 从简单页面入手

为什么要从简单页面入手？

https://medium.com/flutter-community/add-flutter-to-existing-android-ios-app-ae8c4fb1582e

[Flutter原理与实践 - 美团技术团队](https://tech.meituan.com/2018/08/09/waimai-flutter-practice.html)

# 经验

https://book.flutterchina.club/chapter1/flutter_intro.html

- 学习 [dartdoc.dart 源码](https://github.com/flutter/flutter/blob/master/dev/tools/dartdoc.dart)
- 翻译 [dart.cn 文档](https://www.sunmoonblog.com/2019/11/13/flutter-series-1-begin/#)





https://www.pluralsight.com/courses/creating-layouts-flutter?exp=1

书籍

https://www.tutorialspoint.com/flutter/index.htm

https://flutterlearn.com/courses/basic-course