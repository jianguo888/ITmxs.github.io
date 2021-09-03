---
title: "Flutter中的PhysicalModel小部件"
date: 2021-08-15T12:09:10+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

![img](https://miro.medium.com/max/1400/1*ZSez7P9nLpytK7HwIEd3-w.jpeg)

目前，我们所有的应用程序都试图按照设计、流畅性和用户友好体验显示为真实项目。

我们都专注于设计的主要事物，因为我们都知道并相信“所见即所得”这句话。

那么，
让我们来主要的一点，大家都试图去升艾弗真正的设计在我们的网站或应用程序，该用户将互动，并填补这一产品看起来很真实。
为此，我们使用了很多东西，例如项目高度、动画阴影效果等。
在 Flutter 中，有多种方法可以实现这种设计，我们正在讨论其中一个小部件，它很有帮助，这个小部件是**PhysicalModel小部件**。

![img](https://miro.medium.com/freeze/max/60/1*t6JxxahEai6-jwQSfG9RSA.gif?q=20)

![img](https://miro.medium.com/max/600/1*t6JxxahEai6-jwQSfG9RSA.gif)

我们使用 PhysicalModel 实现的主要目的是，

1. 自定义Elevation
2. 自定义阴影
3. 自定义不透明度

我们都知道有多种方法可以在任何小部件中使用高程和阴影，但主要问题是这些选项没有提供如此多的自定义效果。
让我们看看为什么 PhysicalModel 在这些效果中发挥着魅力。



```dart
return Scaffold(
  body: Center(
    child: PhysicalModel(
      elevation: 20.0,
      shadowColor: Colors.red,
      color: Colors.red,
      child: new Container(
        width: 200,
        height: 200,
        decoration: new BoxDecoration(
          shape: BoxShape.rectangle,
          border: new Border.all(
            color: Colors.red,
            width: 1.0,
          ),
        ),
      ),
    ),
  ),
);
```



*遵循属性，它们在您的小部件中提供自定义，*

1. borderRadius属性
2. clipBehavior 属性
3. 颜色属性
4. elevation 属性
5.  shadowColor 属性
6. 形状属性