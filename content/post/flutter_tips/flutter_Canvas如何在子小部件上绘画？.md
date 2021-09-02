---
title: "Flutter_Canvas如何在子小部件上绘画？"
date: 2021-09-02T13:50:42+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

我试图在子文本框小部件上绘制红点，如下所示:
[![enter image description here](https://i.stack.imgur.com/V7S4E.png)](https://i.stack.imgur.com/V7S4E.png)
为此，我用 `CustomPaint()` 包裹了子小部件。小部件:

```dart
 return CustomPaint(
      painter: DotsPainter(), //draws red dots based on child's size
      child: child, //textbox
    );
```

但结果是这样的:
[![enter image description here](https://i.stack.imgur.com/KTOgF.png)](https://i.stack.imgur.com/KTOgF.png)
如何使 CustomPainter “覆盖”其子小部件？
谢谢。



**最佳答案**

CustomPaint 有 2 个可能的画家:

> When asked to paint, CustomPaint first asks its **painter** to paint on the current canvas, then it paints its child, and then, *after painting its child*, it asks its **foregroundPainter** to paint.


(强调我的)
因此，如果您将画家移至 [foregroundPainter](https://api.flutter.dev/flutter/widgets/CustomPaint/foregroundPainter.html)相反，它应该可以正常工作:

```dart
return CustomPaint(
  foregroundPainter: DotsPainter(), //draws red dots based on child's size
  child: child, //textbox
);
```
