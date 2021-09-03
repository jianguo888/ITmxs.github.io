---
title: "自定义widget"
date: 2021-08-19T23:39:41+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]

---

> 本文主要介绍

<!--more-->





# 在 Flutter 中编写自定义小部件（第 2.b 部分）——ChildSize（无助手）

https://rlesovyi.medium.com/writing-custom-widgets-in-flutter-part-2-b-childsize-no-helpers-61c578c9bbd2



# 在 Flutter 中编写自定义小部件（第 1 部分）——EllipsizedText

https://itnext.io/writing-custom-widgets-in-flutter-part-1-ellipsizedtext-a0efdc1368a8

# 介绍

Flutter 中的声明式 UI 非常好，易于使用，并且尽可能多地使用它是非常诱人的。但很多时候开发人员只是过度使用它 - 以声明式的方式编写所有内容，即使有时任务可以以更命令的方式更有效和更容易理解。

每个人都应该理解的 - 声明式编程和命令式编程之间必须始终保持平衡。每个都有自己的用途，每个任务都比其他任务更亮。

在本系列文章中，我将描述如何通过从头开始创建自定义 Widget 来解决不同的问题。每一个都比前一个复杂一点。

# 快速理论

在查看代码之前，我们需要了解一些基本知识。

`Widget`-仅是一个不可变（优选常数）类，它包含用于配置属性`Elements`和`RenderObjects`。它还负责创建所述`Elements`和`RenderObjects`。重要的是要理解——小部件从不包含状态或任何业务逻辑，只传递它们。

`Element`— 是负责实际 UI 树的实体。它具有对所有子项和（与 不同`Widget`）其父项的引用。`Elements`大部分时间都被重用，除非`key`或被`Widget`改变。因此，如果仅`Widget`更改属性，即使`Widget`分配了new ，`Element`也将保持不变。

`State`— 只不过是一个用户定义的类`Element`，其中也有一些来自其`Element`公开的回调。

`RenderObject`— 负责实际尺寸计算、儿童放置、绘图、触摸事件处理等。这些对象`Views`与 Android 或其他框架中的经典对象最为相似。

为什么我们同时拥有`Elements`和`RenderObjects`？因为效率。各有`Widget`各的`Element`，但只有一部分有`RenderObjects`。由于这一点，可以省略很多布局、触摸和其他层次遍历调用。

# 代码

第一个示例将是一个非常简单的 Widget，它在文本不适合时将其椭圆化。您可能会问，当内置 Text 已经支持省略号时，为什么我们需要这样一个 Widget？答案很简单——到目前为止，它只按单词而不是按字符（https://github.com/flutter/flutter/issues/18761）进行切割。因此，如果您有一个很长的单词结尾——大多数情况下您只会看到这个单词的第一个字母，即使有足够的空间需要填充。

那么让我们开始吧。Flutter 有很多内置的基类和 mixin，它们将有助于构建完全自定义的 Widget。以下是其中一些：

- LeafRenderObjectWidget — 没有孩子
- SingleChildRenderObjectWidget — 有一个孩子
- MultiChildRenderObjectWidget — 有任意数量的孩子

在我们的例子中，我们将使用 LeafRenderObjectWidget，因为我们只需要渲染文本并且没有孩子：

```dart
enum Ellipsis { start, middle, end }

class EllipsizedText extends LeafRenderObjectWidget {
  final String text;
  final TextStyle? style;
  final Ellipsis ellipsis;

  const EllipsizedText(
    this.text, {
    Key? key,
    this.style,
    this.ellipsis = Ellipsis.end,
  }) : super(key: key);

  @override
  RenderObject createRenderObject(BuildContext context) {
    return RenderEllipsizedText()..widget = this;
  }

  @override
  void updateRenderObject(BuildContext context, RenderEllipsizedText renderObject) {
    renderObject.widget = this;
  }
}
```

我们已经创建了我们的小部件。唯一不寻常的是存在两种方法：

- createRenderObject — 负责实际创建我们的 RenderObject
- updateRenderObject — 将在 Widget 的数据更改但 RenderObject 保持不变时调用。在这种情况下，我们需要更新 RenderObject 中的数据，否则它将呈现旧文本。

我还需要注意的是，最好将每个值从 Widget 复制到 RenderObject。但是我会传递整个 Widget，因为它们无论如何都是不可变的（而且我懒得写所有的样板代码）。

现在让我们从实际的 RenderObject 开始：

```dart
class RenderEllipsizedText extends RenderBox {
  var _widgetChanged = false;
  var _widget = const EllipsizedText('');

  set widget(EllipsizedText widget) {
    if (_widget.text == widget.text &&
        _widget.style == widget.style &&
        _widget.ellipsis == widget.ellipsis) {
      return;
    }
    _widgetChanged = true;
    _widget = widget;
    markNeedsLayout();
  }
}
```

在这里，我们定义了所有变量并编写了一个 setter 来实际更新它们。还有一个守卫来检查值是否真的改变了——如果没有任何改变，就不需要重新计算省略号和重绘文本。

现在我们需要布局我们的RenderObject。

```dart
class RenderEllipsizedText extends RenderBox {
  // ...
  var _constraints = const BoxConstraints();

  @override
  void performLayout() {
    if (!_widgetChanged && _constraints == constraints && hasSize) {
      return;
    }

    _widgetChanged = false;
    _constraints = constraints;

    size =_ellipsize(
      minWidth: constraints.minWidth,
      maxWidth: constraints.maxWidth,
    );
  }
}
```

布局过程非常简单。我们需要做的就是根据提供给我们的约束计算 RenderObject 的大小。约束仅描述我们必须遵守的最小和最大尺寸。如果没有任何变化并且在之前的布局过程中已经计算了大小，还会添加额外的检查。

创建椭圆文本的实际过程非常繁琐，肯定有更好的解决方案，但我选择使用二分搜索来找到最佳匹配。

```dart
class RenderEllipsizedText extends RenderBox {
  // ...
  final _textPainter = TextPainter(textDirection: TextDirection.ltr);

  Size _ellipsize({required double minWidth, required double maxWidth}) {
    final text = _widget.text;

    if (_layoutText(length: text.length, minWidth: minWidth) > maxWidth) {
      var left = 0;
      var right = text.length - 1;

      while (left < right) {
        final index = (left + right) ~/ 2;
        if (_layoutText(length: index, minWidth: minWidth) > maxWidth) {
          right = index;
        } else {
          left = index + 1;
        }
      }
      _layoutText(length: right - 1, minWidth: minWidth);
    }

    return constraints.constrain(Size(_textPainter.width, _textPainter.height));
  }
}
```

我不会遍历所有这些逻辑（如果您愿意，可以阅读它）。但重要的是 TextPainter 用于计算文本大小。如果文本大小比我们的约束长——我会试着让它越来越短，直到它符合我们的约束。

`_layoutText` 用于计算我们裁剪的文本大小：

```dart
double _layoutText({required int length, required double minWidth}) {
  final text = _widget.text;
  final style = _widget.style;
  final ellipsis = _widget.ellipsis;

  String ellipsizedText = '';

  switch (ellipsis) {
    case Ellipsis.start:
      if (length > 0) {
        ellipsizedText = text.substring(text.length - length, text.length);
        if (length != text.length) {
          ellipsizedText = '...' + ellipsizedText;
        }
      }
      break;
    case Ellipsis.middle:
      if (length > 0) {
        ellipsizedText = text;
        if (length != text.length) {
          var start = text.substring(0, (length / 2).round());
          var end = text.substring(text.length - start.length, text.length);
          ellipsizedText = start + '...' + end;
        }
      }
      break;
    case Ellipsis.end:
      if (length > 0) {
        ellipsizedText = text.substring(0, length);
        if (length != text.length) {
          ellipsizedText = ellipsizedText + '...';
        }
      }
      break;
  }

  _textPainter.text = TextSpan(text: ellipsizedText, style: style);
  _textPainter.layout(minWidth: minWidth, maxWidth: double.infinity);
  return _textPainter.width;
}
```

差不多就是这样。我们剩下要做的就是——实际绘制我们的文本。

```
@override
void paint(PaintingContext context, Offset offset) {
  _textPainter.paint(context.canvas, offset);
}
```

结果如下：



![img](https://miro.medium.com/max/600/1*2Hiya57uQorSz1tMmRW0tA.gif)