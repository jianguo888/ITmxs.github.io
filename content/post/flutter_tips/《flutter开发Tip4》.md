---
title: "《Flutter开发Tip4》"
subtitle: ""
date: 2021-07-30T22:49:09+08:00
lastmod: 2021-07-30T22:49:09+08:00
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



> 本文主要介绍flutter开发小技巧

<!--more-->

## flutter analyse

### 配置步骤

- 在项目更目录添加`analysis_options.yaml`文件可以配置lint规则和analyzer行为

- 具体支持的lint规则参考https://dart-lang.github.io/linter/lints/

- 目前有3类已经定义的常用规则

  > Many lints are included in various predefined rulesets:

  - [pedantic](https://github.com/dart-lang/pedantic) for rules enforced internally at Google
  - [effective_dart](https://github.com/tenhobi/effective_dart) for rules corresponding to the [Effective Dart](https://dart.dev/guides/language/effective-dart) style guide
  - [flutter](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/analysis_options_user.yaml) for rules used in `flutter analyze`

- 推荐使用google团队内部的规则库pedantic

  - 在yaml里面添加依赖 `pedantic: ^1.8.0+1`

- 在`analysis_options.yaml`里面引入使用

  > `include: package:pedantic/analysis_options.1.8.0.yaml`

### 使用

配置好`analysis_options.yaml`文件的规则后,执行`flutter analyse`命令将对你整个项目或者package的代码进行静态分析

### 修复

- 根据提示修复

点击提示的规则，就会跳转到需要修复位置，按照lint规则的说明和例子就可以修正了。[![flutter_analyse_1](https://luckly007.oss-cn-beijing.aliyuncs.com/img/flutter_analyse_2.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_analyse_2.png)

- 利用VSCode快速修复

在提示有问题的代码的地方`Ctrl +.`, 就会自动弹出快速修复，比如图中为增加`const`标识。 是不是快多了。[![flutter_analyse_1](https://luckly007.oss-cn-beijing.aliyuncs.com/img/flutter_analyse_2.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_analyse_1.png)[![屏幕快照 2020-09-17 上午2.24.55](https://luckly007.oss-cn-beijing.aliyuncs.com/img/flutter_analyse_2.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_analyse_2.png)

### 参考配置

当然你还可以根据你的需要定制自己的静态分析规则，下面是最近使用的一套配置，仅供你参考：

> [analysis_options.yaml参考配置](https://gist.github.com/wangruofeng/1451c04c0149fc229a726d819b3135c2)

## dartfmt 命令

- `dartfmt ./ -w` 静态分析代码，并尝试自动修复
- `dartfmt ./ -n` 静态分析代码，不会修复代码

## DevTool介绍

flutter提供了一个devTool系列工具，方便开发者进行调试，查看日志，网络请求，性能等信息，更多信息请参考[DevTools](https://flutter.dev/docs/development/tools/devtools/overview)

### Install DevTools

- [Install from command line](https://flutter.dev/docs/development/tools/devtools/cli)
- [Install and run DevTools from Android Studio](https://flutter.dev/docs/development/tools/devtools/android-studio)
- [Install from VS Code](https://flutter.dev/docs/development/tools/devtools/vscode)

### Launch the DevTools application server

启动本地web服务

```
pub global run devtools   # If you have `pub` on your path.
```

or

```
flutter pub global run devtools   # If you have `flutter` on your path.
```

在命令行，你应该看到类似下面输出，表明服务启动成功

```
Serving DevTools at http://127.0.0.1:9100
```

### Start an application to debug

运行app

```
cd path/to/flutter/app
flutter run
```

一旦你成功运行起来你的app，你将在终端控制台看到如下类似的信息

[![image-20200917151951722](https://luckly007.oss-cn-beijing.aliyuncs.com/img/flutter_analyse_2.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_01.png)

用浏览器打开里面的地址，格式如下

> http://127.0.0.1:59681/fVO-_3yziG8=/

打开将会看到下面的调试面板，可以根据自己的需要进行调试

[![image-20200917152140975](https://luckly007.oss-cn-beijing.aliyuncs.com/img/flutter_analyse_2.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_02.png)

这里有一个很有用的功能，【debug】

打开页面可以查看断点的帧和控制台的日志。

[![image-20200917152456458](https://luckly007.oss-cn-beijing.aliyuncs.com/img/flutter_analyse_2.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_03.png)

### Open DevTools and connect to the target app

一旦工程运行起来了，在浏览器打开[http://localhost:9100，打开DevTools完整版本](http://localhost:9100，打开DevTools完整版本/)

[![image-20200917152646210](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_04.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_04.png)

输入上面的地址，点击Connect，跳转到完整devTools

[![image-20200917152806254](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_05.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_05.png)

这里的功能非常强大和具体使用可以参考官方文档

## flutter 调试

这里以VSCode为例

### 纯flutter项目

纯flutter项目比较简单，VSCode支持各种devtool，使用debug模式运行app，或者直接按F5

[![image-20200917150502934](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_06.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_06.png)

运行成功后会出现一下工具条

[![image-20200917150559335](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_07.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_07.png)

控制台输出以下内容

[![image-20200917150622481](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_08.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_08.png)

例如这里我们在这个文件107行直接下一个断点，代码执行到时会触发断点

[![image-20200917150821892](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_09.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_09.png)

此时工具栏变成下面的样式

[![image-20200917150846708](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_10.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_10.png)

可以通过相关的按钮进行断点调试

### 混合项目(原生+flutter)

1. 打开已经编译运行过的原生端App

2. 通过VSCode打开壳工程，进入壳工程根目录

3. 选择main.dart文件

4. 执行cmd+shift+p,呼出VSCode命令面板，输入`>attach to flutter onDevice`

   [![image-20200917153401365](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_11.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_11.png)

   选择下面的对应的命令执行

   > 如果attach不上请断开wifi重试

5. Attach 成功的话会显示下面一条悬浮工具栏和纯flutter相比里面多了一个类似插头的按钮

   [![image-20200917153656732](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_12.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_12.png)

   Debug Console控制台也会输出下面信息

   [![image-20200917153719698](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_13.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_13.png)

6. 剩下的调试和上面DevTool介绍的流程一致

7. 在这个项目里面可以直接对引用到的代码进行断点调试

   [![image-20200917154306797](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_14.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_14.png)

## 参考

- [analyzer](https://pub.dev/packages/analyzer)
- [dartfmt](https://dart.dev/tools/dartfmt)
- [Flutter Analysis Options](https://juejin.im/post/6844904176162439182#heading-11)
- [Customizing static analysis](https://dart.dev/guides/language/analysis-options)
- [How to access assets in package](https://stackoverflow.com/questions/63914819/how-to-access-assets-in-package)
- [DevTools](https://flutter.dev/docs/development/tools/devtools/overview)

 