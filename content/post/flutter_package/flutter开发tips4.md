---
title: "flutter开发tips4"
date: 2021-08-18T14:05:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]
---



# Auto Size Text

一个简单的小工具，**自动调整**你的**文字**，以适应屏幕。您可以在许多领域使用它，例如文本字段或一般文本。这只是您可以做的许多示例之一

```
AutoSizeText(
  'The text to display',
  style: TextStyle(fontSize: 20),
  maxLines: 2,
),
```





# Font Awesome Flutter

很多开发者都应该知道。它使您可以访问 1500 多个附加图标。这是一个小例子：

```
IconButton(
  icon: FaIcon(FontAwesomeIcons.gamepad),
  onPressed: () {
    print('Pressed');
  }
)
```



# Flutter Markdown

您可以在 Flutter 中**渲染**[Markdown](http://pub.dev/packages/flutter_markdown)**文件**。它支持原始格式，但不支持内联 HTML。

```

```



# PDF

您可以在 Dart 中**创建 PDF 文件**。最棒的是它适用于任何平台，因此也非常适合 Web 开发。你可以[在这里](https://davbfr.github.io/dart_pdf/#/)找到一个非常好的现场演示。

```

```



# Liquid Swipe

它将**Liquid Swipe 动画**带入Flutter。

```
@override
Widget build(BuildContext context) {
  return new MaterialApp(
    home: Builder(
      builder: (context) =>
        LiquidSwipe(
          pages: pages
        ),
    ),
  );
}


final pages = [
  Container(...),
  Container(...),
  Container(...),
];
```

# Curved Navigation Bar

您可以轻松创建一个非常**漂亮的导航栏**，这是一个真正的引人注目的地方。具有非常好的动画和许多自定义选项，这是您下一个应用程序的一个非常好的扩展！

```
Scaffold(
  bottomNavigationBar: CurvedNavigationBar(
    backgroundColor: Colors.blueAccent,
    items: <Widget>[
      Icon(Icons.add, size: 30),
      Icon(Icons.list, size: 30),
      Icon(Icons.compare_arrows, size: 30),
    ],
    onTap: (index) {
//Handle button tap
    },
  ),
  body: container(color: Colors.blueAccent),
)
```

# Rive

您可以创建可以**与应用程序交互的****动画**。

```
RiveAnimation.network(
  '<https://cdn.rive.app/animations/vehicles.riv>'
),
```



# Lottie

# Image Cropper

您可以**在应用程序中裁剪图像**。如果您正在构建一个希望用户裁剪个人资料图片的应用程序或者您想开发一个照片编辑应用程序，这当然很有用。

```
import 'package:image_cropper/image_cropper.dart';
File croppedFile =await ImageCropper.cropImage(
  sourcePath: imageFile.path,
  aspectRatioPresets: [
    CropAspectRatioPreset.square,
    CropAspectRatioPreset.ratio3x2,
    CropAspectRatioPreset.original,
    CropAspectRatioPreset.ratio4x3,
    CropAspectRatioPreset.ratio16x9
  ],
  androidUiSettings: AndroidUiSettings(
      toolbarTitle: 'Cropper',
      toolbarColor: Colors.deepOrange,
      toolbarWidgetColor: Colors.white,
      initAspectRatio: CropAspectRatioPreset.original,
      lockAspectRatio:false),
  iosUiSettings: IOSUiSettings(
    minimumAspectRatio: 1.0,
  )
);
```

# Video Player

视频通常是应用程序中的重要组成部分。因此，在应用程序中**播放视频**应该很容易