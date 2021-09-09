---
title: "避免将AnimationController与setState一起使用"
date: 2021-09-09T09:15:09+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

在 Flutter 中创建动画真的很有趣也很容易，但是有一种不好的做法可能会使这些动画滞后：`setState`.

# 坏习惯：AnimationController 和 setState

`AnimationController`在 a 中使用时`StatefulWidget`，您可能会尝试执行以下操作：

```
void initState() {
  _controller = AnimationController(
    vsync: this,
    duration: Duration(seconds: 1),
  )
  ..addListener(() => setState(() {}));
}
```

要将 a`Placeholder`从左到右设置为100 像素的动画：

```
Transform.translate(
  offset: Offset(100 * _controller.value, 0),
  child: Placeholder(key: Key('animated')),
),
```

这将`Placeholder`通过它也将重建所有其他小部件的动画`StatefulWidget`：

```
Column(
  children: [
    Placeholder(key: Key('1')), // rebuilds
    Placeholder(key: Key('2')), // rebuilds
    Placeholder(key: Key('3')), // rebuilds
    Transform.translate( // rebuilds
      offset: Offset(100 * _controller.value, 0),
      child: Placeholder(key: Key('animated')),
    ),
  ],
),
```

这是构建时间表*[1]*：

![img](https://miro.medium.com/max/2764/0*l4J5DYeHpuz37l5u)AnimationController 和 setState

这些`Placeholder`小部件很轻，但在生产应用程序中，如果有更大的小部件，可能会出现性能问题。

# 良好实践：没有 setState 的 AnimatedBuilder

首先，您需要删除`..addListener(() => setState(() {}))`：

```
void initState() {
  _controller = AnimationController(
    vsync: this,
    duration: Duration(seconds: 1),
  ); 
  // No addListener(...)
}
```

然后将您的过渡包装成一个`AnimatedBuilder`：

```
AnimatedBuilder(
  animation: _controller,
  builder: (_, child) { 
    return Transform.translate(
      offset: Offset(100 * _controller.value, 0),
      child: child,
    );
  },
  child: Placeholder(key: Key('animated')),
),
```

就这样。因为我们已经删除`setState`了`StatefulWidget`不是重建。将重建的唯一小部件是`AnimatedBuilder`：

![img](https://miro.medium.com/max/2796/0*felgCFt4HgJ0HOiK)没有 setState 的 AnimatedBuilder

# 不好的做法：AnimatedBuilder 的不透明度

你可以通过改变不透明度和使用来做同样的事情`AnimatedBuilder`：

```
AnimatedBuilder(
  animation: _controller,
  builder: (_, child) {
    return Opacity(
      opacity: _controller.value,
      child: child,
    );
  },
  child: Placeholder(key: Key('animated')), 
),
```

![img](https://miro.medium.com/max/2282/0*y8nHKvnp8BQV1CN5)AnimatedBuilder 的不透明度

改变不透明度是一个非常昂贵的动画，这就是为什么有更好的方法来做到这一点。

# 良好实践：使用 FadeTransition 设置不透明度

有一个针对性能优化的特殊不透明度过渡：

```
FadeTransition(
  opacity: _controller,
  child: Placeholder(key: Key('animated')),
),
```

使用这种方法，永远不会调用构建：

![img](https://miro.medium.com/max/2924/0*niArkop2BaVNWsqA)FadeTransition 不透明度

**构建**阶段通常位于**动画**和**布局**阶段之间——在这里你可以注意到它现在已经消失了。

# 结论

尽量避免像这样使用`setState`with `AnimationController`：

![img](https://miro.medium.com/max/60/0*I5V29KMIwqAiAeZP?q=20)

![img](https://miro.medium.com/max/700/0*I5V29KMIwqAiAeZP)

不要像这样使用 setState

此外，[请查看](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/transitions.dart)Flutter 框架中的[transitions.dart](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/transitions.dart)以了解您可以使用哪些其他优化的过渡。

可以在[此处](https://gist.github.com/tomaszpolanski/a9e02767477848d796f212ca496b835d)找到这些示例的所有源代码。





*边注：*

如果您想知道为什么`Opacity`和`FadeTransition`小部件在时间轴中不可见，那是因为它们不是由备份`Elements`而是`RenderObjects`直接创建以提高性能。

请记住，**构建**阶段来自`Elements`.

[1] 要在[时间轴中](https://medium.com/flutter-io/profiling-flutter-applications-using-the-timeline-a1a434964af3)启用构建阶段分析，请在 Flutter方法中启用[debugProfileBuildsEnabled 标志](https://docs.flutter.io/flutter/widgets/debugProfileBuildsEnabled.html)`main`
