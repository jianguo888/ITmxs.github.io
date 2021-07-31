---
title: "《开发flutter有哪些好用的VsCode插件》"
subtitle: ""
date: 2021-07-30T22:51:18+08:00
lastmod: 2021-07-30T22:51:18+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

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

## 简介

这里整理了日常使用vscode开发flutter相关的插件，也有部分通用类型的插件

## [Flutter](https://marketplace.visualstudio.com/items?itemName=Dart-Code.flutter) & [Dart](https://marketplace.visualstudio.com/items?itemName=Dart-Code.dart-code)

这2个是flutter官方插件，开发flutter装机必备，不用多说。

## [Awesome Flutter Snippets](https://marketplace.visualstudio.com/items?itemName=Nash.awesome-flutter-snippets)

> Awesome Flutter Snippets is a collection snippets and shortcuts for commonly used Flutter functions and classes

这里面包含Flutter中非常常用的代码片段

## [Bracket Pair Colorizer 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)

> A customizable extension for colorizing matching brackets

这个插件可以使成对的括号带上不同颜色，特别适合dart这种嵌套层级很深声明式语言， 这个插件可以很方便的区分括号的开始和截止位置

[![Bracket _Pair_Colorizer_2](https://blog.wangruofeng007.com/images/flutter_plugin/Bracket _Pair_Colorizer_2.jpg)](https://blog.wangruofeng007.com/images/flutter_plugin/Bracket _Pair_Colorizer_2.jpg)

## [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

> Supercharge the Git capabilities built into Visual Studio Code — Visualize code authorship at a glance via Git blame annotations and code lens, seamlessly navigate and explore Git repositories, gain valuable insights via powerful comparison commands, and so much more

最好用的feature是在该[**行**](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens#current-line-blame-)末尾的引人注目的[**当前行责任**](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens#current-line-blame-)注释，可通过[**悬停**](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens#hovers-)获取详细的责任信息，当然还有很多其他的feature，请自行探索。

[![GitLens_01](https://blog.wangruofeng007.com/images/flutter_plugin/GitLens_01.png)](https://blog.wangruofeng007.com/images/flutter_plugin/GitLens_01.png)

git相关的右键菜单

[![GitLens_02](https://blog.wangruofeng007.com/images/flutter_plugin/GitLens_02.png)](https://blog.wangruofeng007.com/images/flutter_plugin/GitLens_02.png)

## [Color Highlight](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)

> Highlight web colors in your editor

web颜色高亮，直接将颜色对应的css显示出来，非常直观

[![color_highlight](https://blog.wangruofeng007.com/images/flutter_plugin/color_highlight.png)](https://blog.wangruofeng007.com/images/flutter_plugin/color_highlight.png)

## [Image Preview](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.vscode-gutter-preview)

> Shows image preview in the gutter and on hover

单鼠标悬浮在带有图片的超链接上会显示预览图片

[![image_preview](https://blog.wangruofeng007.com/images/flutter_plugin/image_preview.png)](https://blog.wangruofeng007.com/images/flutter_plugin/image_preview.png)

## [pubspec assist](https://marketplace.visualstudio.com/items?itemName=jeroen-meijer.pubspec-assist)

> Easily add and update dependencies to your Dart and Flutter project.

下面是官方的演示效果，可以直接命令搜索flutter相关库

[![img](https://i.imgur.com/W2cGuPL.gif)](https://i.imgur.com/W2cGuPL.gif)

## [Material Icon Theme](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)

> Material Design Icons for Visual Studio Code

针对不同的文件icon和文件夹icon设计了一套主题图标

[![file_icons](https://blog.wangruofeng007.com/images/flutter_plugin/file_icons.png)](https://blog.wangruofeng007.com/images/flutter_plugin/file_icons.png)

[![folder_icons](https://blog.wangruofeng007.com/images/flutter_plugin/folder_icons.png)](https://blog.wangruofeng007.com/images/flutter_plugin/folder_icons.png)

## [Error lens](https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens)

> Improve highlighting of errors, warnings and other language diagnostics.

增强了语言中的错误高亮效果，使错误提示在代码后面显示

[![error_lens](https://blog.wangruofeng007.com/images/flutter_plugin/error_lens.png)](https://blog.wangruofeng007.com/images/flutter_plugin/error_lens.png)

## [flutter-stylizer](https://marketplace.visualstudio.com/items?itemName=gmlewis-vscode.flutter-stylizer)

> Flutter Stylizer organizes your Flutter classes in an opinionated and consistent manner.

使flutter中的代码按照下面的方式排序，方便统一约束团队代码的结构

```
"flutterStylizer.memberOrdering": [
    "public-constructor",
    "named-constructors",
    "public-static-variables",
    "public-instance-variables",
    "private-static-variables",
    "private-instance-variables",
    "public-override-methods",
    "public-other-methods",
    "build-method",
],
```

当然这只是默认顺序，具体的顺序可以自行调整,插件安装完成后，通过快捷键格式化当前的文件

## [RESTClient](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)

> REST Client for Visual Studio Code

在VSCode里面直接发送请求，能自动识别`.http`和`.rest`后缀的文件

点击`Send Request`开始发送请求。