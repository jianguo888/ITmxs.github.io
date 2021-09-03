---
title: "在Flutter中更快地加载您的图像资源"
date: 2021-08-30T22:11:11+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

我们可以将图像放在我们的资产文件夹中，但如何更快地加载它们？这是 Flutter 中的一个秘密函数，可以帮助我们做到这一点 — **precacheImage()**

![img](https://miro.medium.com/max/1400/1*fHEjLZ6HcGGnmmZ53_7LGA.png)

很多时候（尤其是在 Flutter Web 中），您的本地资源图像需要花费大量时间在屏幕上加载和渲染！

对于用户的角度来看E本是不好秒 pecially如果图像是屏幕的背景图像。如果图像是您屏幕中的任何组件，我们仍然可以显示微光或其他内容，以便用户知道该图像正在加载。但是我们不能对背景图像显示微光！

我们在 Flutter 中有一个简单而有用的方法，我们可以用它来更快地加载我们的资产图像**——precacheImage()！**

[**precacheImage**](https://api.flutter.dev/flutter/widgets/precacheImage.html) 将 ImageProvider 和 context 作为必需参数并返回 Future<void>

```
Future<void> precacheImage( 
    ImageProvider<Object> provider, 
    BuildContext context, 
    {Size? size, 
    ImageErrorListener? onError} 
)
```

此方法将图像预取到图像缓存中，然后无论何时使用该图像，它的加载速度都会快得多。但是，ImageCache 不允许保存非常大的图像。

由于在此需要上下文，因此我们可以在可访问上下文的任何函数中添加 precacheImage()。我们可以将相同的内容放在第一个屏幕的**didChangeDependencies()**方法中！

例子：

```
void didChangeDependencies()
{ precacheImage(AssetImage("assets/logo.png"), context); 

precacheImage(AssetImage("assets/home_bg.png"), context);
super.didChangeDependencies();  
}
```

上面的例子将缓存`logo.png`并`home_bg.png`放入ImageCache。所以现在，无论何时我们使用这个图像，它都会加载得更快！

## 结论

这是一个方便的提示，可以更快地加载您的图像资源！这是一个关于使用和不使用**precacheImage()**加载图像所需时间的小统计数据

![img](https://miro.medium.com/max/1104/1*XNYOmLeF9QOMDAR6NPSc4Q.png)

你可以看到，开始的 3 个打印语句是没有 precacheImage 的，每次都花费近 10 毫秒。现在，下一个是 precacheImage，它在缓存中存储图像需要 14 毫秒。随后的加载只用了 5 毫秒。所以我们可以得出结论，它将加载时间减少到近 50%！您可以在[GitHub](https://github.com/AbhishekDoshi26/precache_image_example)上[找到](https://github.com/AbhishekDoshi26/precache_image_example)相同的代码！