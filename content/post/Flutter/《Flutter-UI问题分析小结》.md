---
title: "《Flutter UI问题分析小结》"
subtitle: ""
date: 2021-07-31T19:06:55+08:00
lastmod: 2021-07-31T19:06:55+08:00
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

我们的 Flutter 项目中自定义 AppBar 时 SafeArea 误用导致出现一个奇怪的 UI 问题。本文从这个问题出发，简单梳理了 Android 和 iOS 平台在 AppBar 视觉规范上的差异，然后使用 Dart DevTools 分析并成功解决问题。



[TOC]

# 问题背景

## 需求描述

最近我们项目接入 Flutter 并重写一些简单页面。项目中很多 Native 页面的 AppBar (为简单起见，本文将 Android 的 ActionBar/Toolbar 以及 iOS 的 Navigation Bar 统称为 AppBar)有类似这样的交互效果，

![组队排行榜](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/ezgif1e54ad03a7341.gif)

Flutter 页面也需要实现这种交互。一种做法是 Flutter 页面中保留原有的 Native AppBar，在 Flutter 列表滚动时通过 `MethodChannel` 通知 Native AppBar 更新其背景。不妨称这个方案为 Native 方案。另外一种做法是隐藏原有 Native AppBar，直接在 Flutter 层实现相同交互。这个方案称为 Flutter 方案。

显然，Native 方案实现起来较为繁琐，且似乎偏离了 `MethodChannel` 的合理使用场景。而 Flutter 方案可以避免跟 Native 层不必要的通信，所以我们选择了该方案。

## Flutter 方案

[FortnightlyDemo](https://github.com/flutter/flutter/tree/master/examples/flutter_gallery/lib/demo/fortnightly) (Flutter 官方 example)实现了自定义的 `ShortAppBar`：

![ShortAppBar -w396](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725848702985.jpg)

`ShortAppBar` 简化后的代码如下。

```
class FortnightlyDemo extends StatelessWidget {
  static const String routeName = '/fortnightly';

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Fortnightly Demo',
      theme: _fortnightlyTheme,
      home: Scaffold(
        body: Stack(
          children: <Widget>[ FruitPage(), SafeArea(child: ShortAppBar()),],
        ),
      ),
    );
  }
}

class ShortAppBar extends StatelessWidget {
  const ShortAppBar({ this.onBackPressed });

  final VoidCallback onBackPressed;

  @override
  Widget build(BuildContext context) {
    return SizedBox(
      height: 56,
      ...
    );
  }
}
```

注意这里一个细节，从视觉效果上来看这里的 `ShortAppBar` 并没有延伸到 Status Bar 下方，

![ShortAppBar 没有延伸到 Status Bar 下方](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15726104959036.jpg)

参考 `ShortAppBar` 的代码并加上必要的交互，我很快就实现了 `CustomAppBar`。
而我们项目中要求 `CustomAppBar` 能延伸 Status Bar 下方。给 `SafeArea` 套上一个 `Container` 就可以实现这种效果。

```
Container(
  color: appBarColor,
  child: SafeArea(child: CustomAppBar())
)
```

![ShortAppBar 延伸到 Status Bar 下方](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15726105059869.jpg)

## 遇到的问题

`CustomAppBar` 在 Android 上的效果还不错，在 iPhone 7 上也能接受(高度偏大一点点)，但在 iPhone 11 上的实际效果显得很违和了：

![CustomAppBar 在 iPhone 11 上偏高 -w301](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725908638519.jpg)

![CustomAppBar 比原生应用的 AppBar 要高 -w331](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725911634410.jpg)

如上图，`CustomAppBar` 在 iPhone 11 上比原生应用的 AppBar 严重偏高。

# 问题分析

为什么 `CustomAppBar` 在 Android 机器上看起来很好，在 iOS (尤其是 iPhone 11)上却严重偏高？

Flutter 可以在 Android 平台和 iOS 平台上在像素级别无差别地进行绘制，这是它的强大之处。但不容忽视的是，Android 视觉规范天生跟 iOS 有差异，如果不考虑并尊重这些差异而强行无差别绘制，肯定会导致某一端看起来怪怪的。

所以很显然问题的一个原因是 `CustomAppBar` 的实现没有考虑平台差异。另一个原因则是 `SafeArea` 的误用导致 `CustomAppBar` 进一步偏高。接下来就这两个原因分别展开。

## 视觉规范的差异

首先得承认 Android 和 iOS 视觉上天生存在某些不一致。 (这也是 Flutter 分别提供 material 和 cupertino 风格控件的原因)

根据 [iOS Navigation Bar 设计规范](https://developer.apple.com/design/human-interface-guidelines/ios/bars/navigation-bars/)，标准的 AppBar 高度是 44pt (不包括 Status Bar)。

根据 [Android Material Design 设计规范](https://material.io/develop/android/)，Status Bar 高度是 24dp，标准的 AppBar(Action Bar) 高度是 56dp。

[Gitme](https://github.com/flutterchina/gitme) 是用 Flutter 实现的应用，不过它是 Material Design 风格的，在 iOS 上看起来是这样：

![Gitme -w359](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725930597849.jpg)

总觉得哪里怪是不是？没错，这里的 AppBar 太高！

所以第一个改进就是根据当前系统为 `CustomAppBar` 选择合适的高度。修改后的示意代码如下：

```
class CustomAppBar extends StatelessWidget {
  const CustomAppBar({ this.onBackPressed });

  final VoidCallback onBackPressed;

  @override
  Widget build(BuildContext context) {
    return SizedBox(
      height: isAndroid ? 56 : 44,
      ...
    );
  }
}
```

------

事实上，Flutter 分别为 material 和 cupertino 提供了符合各自设计规范的 [AppBar](https://flutter.dev/docs/catalog/samples/basic-app-bar) 和 [CupertinoNavigationBar](https://api.flutter.dev/flutter/cupertino/CupertinoNavigationBar-class.html)，以简化应用开发。

你可能会问，既然有了 AppBar 和 CupertinoNavigationBar，能不能这样处理？

```
class AnotherCustomAppBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    if (isAndroid) {
        return AppBar();
    } else if (isIOS) {
        return CupertinoNavigationBar();
    } else {
    }
  }
}
```

`AnotherCustomAppBar` 理论上更简单优雅，但实践发现有以下问题：

- `AppBar` 的高度是 56dp，而我们Android端应用的规范是 50dp，所以需要调整 `AppBar` 高度
- `AppBar` 在 Android 端缺并不是沉浸式风格(status bar 和 AppBar 背景色不同)，所以需要对其进行适配
- `CupertinoNavigationBar` 背景色设置成透明仍然会遮挡背景图片 (why?)

![Android 上 status bar 有不同背景色 -w295](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15730062923455.jpg)

![iOS 上背景图被 AppBar 遮挡 -w770](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725942471237.jpg)

再考虑到后期可能还会给自定义的 AppBar 添加更多功能，直接使用 Flutter SDK 提供的 `AppBar` 和 `CupertinoNavigationBar` 相对不可控，所以放弃了 `AnotherCustomAppBar` 方案。

------

## SafeArea 的误用

视觉规范差异能在一定程度解释 `CustomAppBar` 在 iOS 上高度过大的问题。但考虑到其实这个差异不太大 (56-44=12，应该只相差12个逻辑单位)，跟视觉上看到的高度偏差有所不符，所以仍然让人疑惑是否存在其他问题。

![AppBar 过高 -w301](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725908638519.jpg)

肉眼感觉这里的 AppBar 怕不止 56pt 吧? 不过肉眼不靠谱，还是上工具实测。

### UI 分析

[DevTools](https://flutter.dev/docs/development/tools/devtools/overview) 是 Dart 和 Flutter 下的性能测试与 bug 调试的工具。它的功能包括：

- 检查 Flutter UI 布局
- 检查 Flutter app 性能问题
- 源码调试
- 内存诊断

我们这里重点关注检查 Flutter UI 布局的功能。用法如下：

第一步，通过 `flutter attach` 连上 App 后，从 IDE 打开 DevTools。VS Code 使用 ⇧⌘P 调出如下窗口然后选择 “Open DevTools”

![Dart DevTools -w629](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725956009978.jpg)

第二步，在打开的浏览器窗口中按以下步骤操作就可以检查目标 Widget。

![Select Widget Mode -w1421](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725954425771.jpg)

我们感兴趣的 Widget 是 `CustomAppBar`。以下三张图从不同角度近距离观察它。

![CustomAppBar 真实高度](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/safeareacustomappbarisok.jpg)

图一，从图中可以看出 `CustomAppBar` 的高度其实没有问题，正是预期中的 44pt (`CustomAppBar` 高度由 `CustomAppBarSize` 限制)

------

![CustomAppBar 可见高度 -w1117](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725961591573.jpg)

图二，从图中可以看出 `CustomAppBar` 的高度看似偏高很多。

- 可以看到红色框中的 `SafeArea` 为 `CustomAppBar` 添加了一个不正常的 `Padding`，bottom padding 为 34
- `Padding` 本身是不可见的，所以通常不会有影响。不过 `SafeArea` 的父节点是一个可见的 `Container`，所以看起来 AppBar 偏高

------

![CustomAppBar 高度受 padding 影响 -w1096](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725963633326.jpg)

图三，图中的 `CustomAppBar` 的高度正常。

- `SafeArea` 同样为 `CustomAppBar` 添加了 `Padding`。不过这一回，bottom padding 为0
- `SafeArea` 的父节点同样是一个可见的 `Container`，但由于 bottom padding 为0，所以看起来 AppBar 高度正常

至此可以初步定位到问题跟 `SafeArea` 有关(我们使用 `SafeArea` 来适配刘海屏设备)。继续分析前先快速了解下 [SafeArea](https://api.flutter.dev/flutter/widgets/SafeArea-class.html)。如果你熟悉这个 Widget 建议直接略过下一节。

### SafeArea 介绍

再来说说 SafeArea。iPhone X 首次使用刘海屏，并且屏幕的可显示区域也不再是方方正正的了。这就带来如下一个问题：

![异形屏导致内容显示不全 -w345](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15730072986793.jpg)

所以也就有了 SafeArea。这个[视频](https://www.youtube.com/watch?v=lkF0TQJO0bA)简单生动地介绍了 [SafeArea](https://api.flutter.dev/flutter/widgets/SafeArea-class.html)。

`SafeArea` 的代码很少，所以这里就直接贴上来了。

```
/// A widget that insets its child by sufficient padding to avoid intrusions by
/// the operating system.
///
class SafeArea extends StatelessWidget {

  /// Whether to avoid system intrusions on the left.
  final bool left;

  /// Whether to avoid system intrusions at the top of the screen, typically the
  /// system status bar.
  final bool top;

  /// Whether to avoid system intrusions on the right.
  final bool right;

  /// Whether to avoid system intrusions on the bottom side of the screen.
  final bool bottom;
  final Widget child;

  @override
  Widget build(BuildContext context) {
    assert(debugCheckHasMediaQuery(context));
    final MediaQueryData data = MediaQuery.of(context);
    EdgeInsets padding = data.padding;
    // Bottom padding has been consumed - i.e. by the keyboard
    if (data.padding.bottom == 0.0 && data.viewInsets.bottom != 0.0 && maintainBottomViewPadding)
      padding = padding.copyWith(bottom: data.viewPadding.bottom);

    return Padding(
      padding: EdgeInsets.only(
        left: math.max(left ? padding.left : 0.0, minimum.left),
        top: math.max(top ? padding.top : 0.0, minimum.top),
        right: math.max(right ? padding.right : 0.0, minimum.right),
        bottom: math.max(bottom ? padding.bottom : 0.0, minimum.bottom),
      ),
      child: MediaQuery.removePadding(
        context: context,
        removeLeft: left,
        removeTop: top,
        removeRight: right,
        removeBottom: bottom,
        child: child,
      ),
    );
  } 
}
```

本质上来说，`SafeArea` 会主动为其 child 加上 `Padding` 以避免 UI 超出屏幕。

- `SafeArea` 的四个属性 `left`,`top`,`right`, `bottom` 分别用于控制是否对 child 的各个边界做 safe 处理
- 需要特别注意的是，**这四个属性默认是 `true`**。

### 问题小节

为了适配 iPhone X 等设备，我们会为 `CustomAppBar` 给添加 `SafeArea` 以避免内容显示不全。为了保证 `CustomAppBar` 能延伸到 Status Bar 下方，又在最外层加上了 `Container` 。适配代码如下：

```
Container(
  child: SafeArea(
    child: CustomAppBar())
)
```

`SafeArea` 底层调用 `MediaQuery.of` 来构建合理的 `Padding` 对象。一开始我并没有注意到 `MediaQuery.of(context).padding.bottom` 的返回值在不同机型上有区别。比如，由于是带圆角的屏幕，在 iPhone 11 上 `MediaQuery.of(context).padding.bottom` 返回值为 `34pt`。而在方形屏幕上该值为 `0pt`。见下图：

![iPhone 11 上 padding.bottom 的返回值 -w908](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725981523931.jpg)

另一方面，我没有注意到 `SafeArea.bottom` 值默认为 `true`。所以这里的 `padding.bottom` 返回的 `34pt` 被错误地用作 `CustomAppBar` 的 bottom padding，而最外层带红色背景的 `Container` 会让 `Padding` 占据的区域变得可见。

最终，各种因素的综合影响导致 `CustomAppBar` 高度看起来严重偏大，如下图：

![CustomAppBar 偏高 -w301](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/11/06/15725908638519.jpg)

找到原因后，对代码进行第二个改进：指定 `SafeArea.bottom` 为 `false`。至此，问题解决！

```
SafeArea(
  bottom: false,  
  child: CustomAppBar()
)
```

------

**补充**：为什么 `CupertinoNavigationBar` 不会有高度问题，它是怎么处理的？

我翻了下源码，果然也是将 `bottom` 设置为 `false`。

```
// flutter/packages/flutter/lib/src/cupertino/nav_bar.dart
...
return SizedBox(
  height: _kNavBarPersistentHeight + MediaQuery.of(context).padding.top,
  child: SafeArea(
    bottom: false,
    child: paddedToolbar,
  ),
);
```



这从另一个角度印证了前面的分析以及解决方法是正确的。

------

# 总结

本文记录了 Flutter 项目中自定义 AppBar 时遇到的一个 UI 问题。问题主要原因在于：

- Android 和 iOS 视觉规范的差异
- `SafeArea` 的误用

先从讨论了Android 和 iOS 视觉规范的差异对 Flutter UI 开发可能存在的影响，并使用 [Dart DevTools](https://flutter.dev/docs/development/tools/devtools/overview) 对 `SafeArea` 误用问题进行分析。

再梳理了 `SafeArea` 的误用是如何影响 `CustomAppBar` 的高度的。

- 首先，`SafeArea` 缺省的 `bottom` 为 `true`
- 第二，iPhone 11 上 `MediaQuery.of(context).padding.bottom` 返回值为 `34pt`
- 最后，`Container` 导致由 `SafeArea` 添加的 `Padding` 占据的区域可见

基于上述分析对 `CustomAppBar` 的进行两处改进，

- 根据当前系统为 `CustomAppBar` 选择合适的高度

- 确保正确使用

   

  ```
  SafeArea
  ```

  ，避免产生不正确的可见区域。记住这两点，

  - `SafeArea` 会为内层的 child 添加 `Padding`
  - `SafeArea` 外层的 `Container` 会导致 `Padding` 占据的区域可见

通过这两处改进，成功解决自定义 AppBar 高度过大的问题，完美适配 iPhone 11。