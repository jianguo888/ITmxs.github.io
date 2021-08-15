---
title: "Flutter中的ImageFilter小部件"
date: 2021-08-15T12:14:36+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

在本文中，我们将讨论**ImageFiltered**类，它用作图像模糊、变换任何像素，或者换句话说，我们称为矩阵变换。

ImageFiltered 是一个获取像素并注册它们的低级类，它是一个小而强大的小部件，可让您对应用程序中的任何内容进行模糊或像素转换。它通常是**BackdropFilter**的更好替代品。

现在让我们讨论使用它。
第一步是，我们像这样导入

导入 'dart:ui';

导入后，我们有许多可用于小部件定制的选项，

因此，让我们讨论其中的一些。

```dart
ImageFiltered( 
          imageFilter: ImageFilter.blur(sigmaX: 5), 
          child: Center( 
            child: FlutterLogo( 
              size: 220, 
            ), 
          ), 
        ), 
```

其次是像这样的任何**矩阵**变换（缩放，平移，旋转，squing），

```dart
ImageFiltered( 
          imageFilter: ImageFilter.matrix(Matrix4.rotationZ(0.2).storage), 
          child: Center( 
            child) : FlutterLogo(
              大小: 220, 
            ), 
          ), 
        ),
```

ImageFiltered 不仅适用于图像，而且我们可以将其应用于任何小部件。它类似于**BackdropFliter**。但它的性能较差

所以 ImageFiltered 是一种在我们的应用程序中模糊或转换小部件像素的简单方法。

所以下次你可以在你的应用程序中尝试这个小部件时，它很棒而且很有魅力。