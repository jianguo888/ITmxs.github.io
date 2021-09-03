---
title: "在Flutter中检测滑动"
date: 2021-08-15T19:49:40+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

想要根据用户滑动导航到屏幕？例如，如果用户从左向右滑动，则导航到下一个屏幕，如果用户从右向左滑动，则导航到上一个屏幕。（类似于iOS 😉）。让我们看看你如何做到这一点！

![img](https://miro.medium.com/max/700/0*IU8v5lkYJ23ulijY.png)

Flutter 框架提供了一个很棒的小部件和我最喜欢的**GestureDetector 之一！**它帮助开发人员检测用户对特定小部件的交互。让我们看看如何检测滑动。

为了仅检测小部件上的tap，我们有`onTap`回调。只需用`GestureDetector`. 例如：

```
GestureDetector( 
  onTap: () { 
      print('Widget Tapped'); 
    }, 
    child: Container(), 
),
```

**onTap**使用起来非常简单，对开发人员来说很方便。

`GestureDetector`，我们有许多可用于检测滑动的回调。但是，水平和垂直滑动有 2 个回调，它们非常易于使用和理解。这两个分别是`onHorizontalDragUpdate`和`onVerticalDragUpdate`。让我们看看它们是如何工作的！

让我们创建一个小应用程序，用户可以在 Page1 上从右向左滑动以导航到 Page2。在Page2上，用户可以从左向右滑动来弹出当前页面并导航到Page1！（类似于iOS的东西）。

要检测从右向左滑动，我们可以使用`onHorizontalDragUpdate`如下：

```
onHorizontalDragUpdate: (details) { 
        if (details.delta.direction > 0) { 
          Navigator.of(context).push(MaterialPageRoute(builder: (context) => Page2())); 
        } 
      },
```

但是，您还应该提及如果用户垂直滑动该怎么办。在这里，我们不会做任何事情，但我们将提供一个空白函数，否则手势会发生冲突。

```
onVerticalDragUpdate: (details) {}, 
onHorizontalDragUpdate: (details) { 
        if (details.delta.direction > 0) { 
          Navigator.of(context).push(MaterialPageRoute(builder: (context) => Page2())); 
        } 
      },
```

现在，我们的 Page1 代码如下所示：

```dart
import 'package:flutter/material.dart';
import 'package:swipe_detection_example/page2.dart';

class Page1 extends StatelessWidget {
  const Page1({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onVerticalDragUpdate: (details) {},
      onHorizontalDragUpdate: (details) {
        if (details.delta.direction > 0) {
          Navigator.of(context).push(MaterialPageRoute(builder: (context) => Page2()));
        }
      },
      child: Scaffold(
        body: Center(
          child: Text('Page 1'),
        ),
      ),
    );
  }
```

现在是检测 Page2 从右向左滑动的时候了。是的，你没看错，我们只需要反转条件！

```
onVerticalDragUpdate: (details) {}, 
onHorizontalDragUpdate: (details) { 
   if (details.delta.direction <= 0) { 
          Navigator.pop(context); 
    } 
},
```

这是 Page2 代码的样子：

```dart
import 'package:flutter/material.dart';
import 'package:swipe_detection_example/page2.dart';

class Page2 extends StatelessWidget {
  const Page2({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onVerticalDragUpdate: (details) {},
      onHorizontalDragUpdate: (details) {
        if (details.delta.direction <= 0) {
          Navigator.pop(context);
        }
      },
      child: Scaffold(
        body: Center(
          child: Text('Page 2'),
        ),
      ),
    );
  }
}
```



现在，让我们看看它的实际效果！！当您将此功能集成到您的应用程序中时，它的行为方式如下：

![img](https://miro.medium.com/max/700/1*jkeFJPP6Qs9QoZSFsDQr2Q.gif)

希望你喜欢这篇文章！