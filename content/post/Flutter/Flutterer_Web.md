---
title: "Flutterer_Web"
date: 2021-08-15T13:12:19+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

![img](https://miro.medium.com/max/1400/0*hFGJsbL4PDw3wGkk.jpeg)

对 Web 开发充满热情？那么你绝对应该尝试 Flutter Web！

- **让我们总结一下我们将在此博客中看到的内容：**
  - 什么是 Flutter Web 以及它是如何工作的？
  - 如何在 Flutter Web 上运行你现有的项目？
  - 如何创建具有 Web 支持的新应用程序？
  - 使用 Flutter Web 时必须注意的几个关键点！
  - Flutter Web 的性能如何，有何不同？
  - 结论

# 什么是 Flutter Web 以及它是如何工作的？

- **Flutter 的 Web**支持使复杂的独立**Web**应用程序具有丰富的图形和交互式内容，以覆盖各种设备上的最终用户。**Flutter 的Web**支持为现有的**Flutter**移动应用程序提供了基于浏览器的交付模型。
- Flutter 以与渲染您的 android/iOS 应用程序相同的方式呈现 Web 应用程序，即 Flutter Web 会在您希望部署时将您的项目转换为原生代码（HTML、CSS、JS）。现在，您需要记住的一件事是 Flutter Web 创建**单页 Web 应用程序！**你当然可以有多个页面，但是当 Flutter 框架将 Web 应用程序转换为本地语言时，将只有一个 html 文件，即**index.html。**
- **那么我们如何拥有多个页面呢？**这个问题的答案非常简单。如果我们仔细研究**Navigator 的**工作原理，我们会发现它适用于`Stack`数据结构。因此，Flutter Web 是单个页面，但它在同一个原生页面上推送多个页面。

![img](https://miro.medium.com/max/1400/0*b6x35BeO8JFJt4j_.png)

# 如何在 Flutter Web 上运行你现有的项目？

- Flutter Web 现在在**Flutter 2.0 的****稳定**频道上**！**
- 但是，如果您不使用 Flutter 2.0，您仍然可以通过运行以下命令来使用它：

```
flutter channel beta
flutter upgrade
flutter config --enable-web
flutter create .
```

- 如果您已经使用 Flutter 2.0，但从未在 Web 上运行过旧项目，则可以运行以下命令集：

```
flutter config --enable-web
flutter create .
```

- 一旦命令成功执行，您的项目就可以在 Web 上点火了！您将能够在项目目录中看到一个**Web**文件夹。
- **现在，要在 Web 上运行您的项目，只需选择 Chrome 或 Edge 作为您的设备，然后单击运行。**

# **如何创建具有 Web 支持的新应用程序？**

- 当我们使用 Flutter 2.0 创建新项目时，新项目已经启用了 Web 支持。
- 但是如果由于某些原因它没有启用，你可以运行以下命令：

```
flutter config --enable-web
flutter create .
```

- 现在您的项目已启用 Web，您可以直接运行它。

# 使用 Flutter Web 时必须注意的几个关键点！

使用 Flutter Web 时，需要牢记以下几点：

- Flutter Web 创建单页应用程序！
- 由于用户现在可以以任何屏幕尺寸打开您的网站，因此您需要使其具有响应性。
- 有很多支持 web 的包，但在编码之前总是检查支持的平台。
- 如果您来自 Web 开发背景，并且如果您想对本机代码进行任何更改，那么非常欢迎您这样做。您可以像更改 Android 和 iOS 一样更改本机代码。
- 当您想要部署您的网络应用程序时，您只需运行：

```
flutter build web
```

您将`web`在项目目录中获得一个文件夹，其中，包括`index.html`

# 使您的 Web 应用程序响应的提示和技巧！

- 使您的 Web 应用程序具有响应性的最佳方法是使用`MediaQuery`获取屏幕的高度和宽度，并在此基础上您可以分配字体大小。
- 还有许多其他软件包可以帮助您使应用程序具有响应性。一些最有用的包是：[responsive_framework](https://pub.dev/packages/responsive_framework)、[flutter_screenutil](https://pub.dev/packages/flutter_screenutil)等。

- # 让我们制作一个可以响应所有屏幕尺寸的小型网络应用程序！

  - 创建一个新项目并删除所有可用代码 `main.dart`
  - 将以下代码粘贴到 main.dart 中：

```
import 'package:flutter/material.dart';void main() => runApp(MyApp());class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Material App',
      home: HomePage(),
    );
  }
}class HomePage extends StatefulWidget {
  const HomePage({Key? key}) : super(key: key);@override
  _HomePageState createState() => _HomePageState();
}class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        height: MediaQuery.of(context).size.height - 100.0,
        width: MediaQuery.of(context).size.width - 100.0,
        color: Colors.orange,
        child: Text(
          'Responsive Text',
          style: TextStyle(fontSize: MediaQuery.of(context).size.width / 10),
        ),
      ),
    );
  }
}
```

- 代码非常简单。有一个 Container 的高度和宽度使用`MediaQuery`. 我们使用维护`Text`的孩子，您可以通过简单地增加和减少浏览器的大小来检查该项目是否响应！

# Flutter Web 的性能如何，有何不同？

- 与原生相比，Flutter Web 的工作非常流畅，因为它只创建单页应用程序，因此在浏览器上的负载更少。
- 使用 Flutter，与原生动画相比，您可以非常轻松地创建一些出色的动画，从而使您的 Web 应用程序更加美观。
- Flutter Web 直接支持将您的网站安装为独立应用程序（Web-App），如果是原生应用程序，您需要为其单独编码。
- Flutter，因为它是跨平台的框架，你可以添加一些特定于平台的代码而无需任何配置更改。

# 结论！

- Flutter Web 应用程序是单页应用程序。
- Flutter 通过将 dart 代码转换为本机代码，呈现与呈现 Flutter Android 或 iOS 应用程序相同的 Web 应用程序。
- 在我们的项目中使用它之前，我们需要检查一个特定的包是否有 Web 支持。
- 始终使您的 Web 应用具有响应性，因为用户可以在任何屏幕尺寸下打开它。