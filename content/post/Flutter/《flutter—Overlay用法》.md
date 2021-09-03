---
title: "《Flutter—Overlay用法》"
subtitle: ""
date: 2021-07-31T18:23:24+08:00
lastmod: 2021-07-31T18:23:24+08:00
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



> 本文主要介绍Flutter Overlay 用法简介。

<!--more-->

# Overlay 基本用法

`Overlay` 实际上是一个 `Stack`，所以 `OverlayEntry` 的内容可以是 `Positioned`。

```
var overlayEntry = OverlayEntry(builder: (context) => Positioned(...););
Overlay.of(context).insert(overlayEntry);
```

- 显示 Overlay - 使用 `Overlay.of(context).insert()` 方法
- 隐藏 Overlay - 使用 `overlayEntry.remove()` 方法

![overlay-basic](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/overlaybasic.gif)

# Overlay 高级用法

## 指定位置

有时我们想指定 Overlay 的显示位置，实现方式如下：

- 先使用 `BuildContext.findRenderObject()` 来找到当前 Widget 对应的 RenderObject
- 再使用 `RenderBox.localToGlobal()` 找到 Widget 在屏幕上的位置

具体见 `_calcPos()` 方法。

```
class _MyButton extends StatefulWidget {
  final PosCallback callback;

  const _MyButton({Key key, this.callback}) : super(key: key);

  @override
  __MyButtonState createState() => __MyButtonState();
}

class __MyButtonState extends State<_MyButton> {
  List<double> _calcPos() {
    RenderBox renderBox = context.findRenderObject();
    var size = renderBox.size;
    var offset = renderBox.localToGlobal(Offset.zero);

    return [offset.dx, offset.dy + size.height];
  }

  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: () {
        List<double> list = _calcPos();
        widget.callback(list[0], list[1]);
      },
      child: Text('show overlay'),
    );
  }
}
```

![overlay-pos](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/overlaypos.gif)

## 跟随滚动

使用 [LayerLink](https://api.flutter.dev/flutter/rendering/LayerLink-class.html) 来让 Overlay 跟随指定的 Widget 来滚动。

- LayerLink 用于联系 [LeaderLayer](https://api.flutter.dev/flutter/rendering/LeaderLayer-class.html) 和 [FollowerLayer](https://api.flutter.dev/flutter/rendering/FollowerLayer-class.html)
- 使用 [CompositedTransformTarget](https://api.flutter.dev/flutter/widgets/CompositedTransformTarget-class.html) 来创建 LeaderLayer
- 使用 [CompositedTransformFollower](https://api.flutter.dev/flutter/widgets/CompositedTransformFollower-class.html) 来创建 FollowLayer

代码篇幅较长，这里就不贴上来了。完整代码见 [gist](https://gist.github.com/410063005/4034c395f3305feeb262482a96d4278e)。

![overlay-scroll](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2020/07/15/overlayscroll.gif)

# 参考

- [Flutter: Using Overlay to display floating widgets | by AbdulRahman AlHamali | SAUGO 360 | Medium](https://medium.com/saugo360/https-medium-com-saugo360-flutter-using-overlay-to-display-floating-widgets-2e6d0e8decb9)
- [LayerLink class - rendering library - Dart API](https://api.flutter.dev/flutter/rendering/LayerLink-class.html)