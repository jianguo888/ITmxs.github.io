---
title: "Elements，Keys和Flutter的性能"
date: 2021-09-09T09:19:02+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

TL;DR：Widget`Keys`可以在您无法获得承诺的*60 FPS 的*地方提高我们的应用程序的性能。

一个 Element 是由 a 在内部创建的`Widget`。它的主要目的是知道小部件树中创建它的小部件的位置。

`Elements`创建起来很昂贵，如果可能，它们应该被重用。这可以通过键(`ValueKeys`和`GlobalKeys`)来实现。

# 元素的生命周期

- Mount——它在元素第一次添加到树中时调用
- Activate — 在激活之前停用的元素时调用
- 更新 —`RenderObject`用新数据更新
- 停用 - 当`Element`从`Widget`树中删除/移动时调用它。一个`Element`仍然可以被激活，如果在同一帧期间被移动，它有一个`GlobalKey`
- Unmount — 如果`Element`在 Frame 期间没有重新激活，它将被卸载并且不能再重复使用

为了提高性能，我们需要尽可能频繁地使用 activate 和 update 并尽量避免触发卸载和挂载。

**重要提示**：大多数情况下您不需要特殊优化，因为 Flutter 很快，当您想要修复可见的性能问题时，我建议使用以下方法。

# 更改列/行中的位置

在第一个示例中，我们要更改红色`Container`和 的`Placeholder`显示顺序：

![img](https://miro.medium.com/max/640/0*1x-blS3694EnQ9u2)

![img](https://miro.medium.com/max/640/0*3bw04atclm5Kqvoe)

```
Widget build(BuildContext context) {
  return Column(
    children: [
      value
          ? const SizedBox()
          : const Placeholder(),
      GestureDetector(
        onTap: () {
          setState(() {
            value = !value;
          });
        },
        child: Container(
          width: 100,
          height: 100,
          color: Colors.red,
        ),
      ),
      !value
          ? const SizedBox()
          : const Placeholder(),
    ],
  );
}
```

当我们点击`GestureDetector` *[1]*时会发生这种情况：

```
08:17:53.652: update Column
08:17:53.666: deactivate Placeholder
08:17:53.679: deactivate GestureDetector
08:17:53.679: deactivate Container
08:17:53.679: deactivate SizedBox
08:17:53.679: mount SizedBox
08:17:53.679: mount GestureDetector
08:17:53.679: mount Container
08:17:53.691: mount Placeholder
08:17:53.691: unmount SizedBox
08:17:53.698: unmount Placeholder
08:17:53.700: unmount Container
08:17:53.715: unmount GestureDetector
```

正如你所看到的，只有`Column`更新了，其余的项目首先被停用，新的`Elements`被安装，然后旧的被处理掉。

让我们看看重建那个元素需要多长时间——为此，我使用了[天文台的](https://flutter.io/docs/testing/debugging#dart-observatory-statement-level-single-stepping-debugger-and-profiler)时间轴功能

![img](https://miro.medium.com/max/2594/1*0n5kOYjuVzjOLFl3N7K0bw.png)

在此图表上，您可以看到成对的时间线：

- 挂载 <widget name> — 例如`Mount Placeholder `— 这是挂载阶段用了多长时间
- <widget name>——例如`Placeholder`——这是构建widget所用的时间

平均而言，构建所有这些小部件需要 5.5 毫秒

# 如何改善这一点？

您可以通过将`ValueKey`s分配给正在卸载的根小部件来改进这一点：

```
Widget build(BuildContext context) {
  return Column(
    children: [
      value
          ? const SizedBox(key: ValueKey('SizedBox'))
          : const Placeholder(key: ValueKey('Placeholder')),
      GestureDetector(
        key: ValueKey('GestureDetector'),
        onTap: () {
          setState(() {
            value = !value;
          });
        },
        child: Container(
          width: 100,
          height: 100,
          color: Colors.red,
        ),
      ),
      !value
          ? const SizedBox(key: ValueKey('SizedBox'))
          : const Placeholder(key: ValueKey('Placeholder')),
    ],
  );
}
```

现在我们有这个日志输出：

```
08:21:37.576: update Column
08:21:37.594: update SizedBox-[<’SizedBox’>]
08:21:37.596: update GestureDetector-[<’GestureDetector’>]
08:21:37.611: update Container
08:21:37.619: update Placeholder-[<’Placeholder’>]
```

时间线输出如下所示：

![img](https://miro.medium.com/max/2308/0*EWfhMdHCYjwu_QSa)

在日志输出和时间线中，您都看不到任何安装。此外，Widget 的平均构建时间为 1.6 毫秒，而之前为 5.5 毫秒*[2]*。

![img](https://miro.medium.com/max/2000/0*Ciz3yyS7wY1lPopj)

# 更改小部件的父级

有时您想将您的 Widget 在屏幕上居中，但是当没有足够的空间时，您想将我们的 Widget 放在一个 a 中，`SingleChildScrollView`这样它就不会溢出。

在这些情况下，您需要更改您`Widget`在 Widget 树中的位置：

```
Widget build(BuildContext context) {
  final inner = MaterialApp(
    home: Container(
      width: 100,
      height: 100,
      color: Colors.red,
    ),
  );
  return GestureDetector(
    onTap: () {
      setState(() {
        value = !value;
      });
    },
    child: value ? SizedBox(child: inner) : inner,
  );
}
```

在这个依赖于 的例子中`value`，我们`MaterialApp`用`SizedBox`小部件包围了一个非常复杂的小部件。

让我们看看日志输出*[1]*：

```
09:41:43.325: update GestureDetector
09:41:43.348: deactivate MaterialApp
09:41:43.350: deactivate Container
09:41:43.352: mount SizedBox
09:41:43.352: mount MaterialApp
09:41:43.425: mount Container
09:41:43.450: unmount Container
09:41:43.476: unmount MaterialApp
```

平均构建时间为 67ms *[2]*。

# 如何改善这一点？

要重用`MaterialApp`小部件，我们需要为其分配一个`GlobalKey`(正常`ValueKey`是不够的)

```
class _GlobalKeyWidgetState extends State<GlobalKeyWidget> {
  bool value = false;
  final global = GlobalKey();

  @override
  Widget build(BuildContext context) {
    final inner = MaterialApp(
      key: global,
      home: Container(
        width: 100,
        height: 100,
        color: Colors.red,
      ),
    );
    return GestureDetector(
      onTap: () {
        setState(() {
          value = !value;
        });
      },
      child: value ? SizedBox(child: inner) : inner,
    );
  }
}
```

时间轴中的日志输出如下：

```
09:56:46.993: update GestureDetector
09:56:47.030: deactivate MaterialApp
09:56:47.060: deactivate Container
09:56:47.060: mount SizedBox
09:56:47.072: activate MaterialApp
09:56:47.095: activate Container
09:56:47.098: update MaterialApp
09:56:47.188: update Container
```

![img](https://miro.medium.com/max/2528/0*7BRAsgdIANPWcITS)

再次在日志输出和时间线中，您没有看到挂载`MaterialApp`，仅`SizedBox`是新添加的。Widget 的平均构建时间为 25 毫秒，而之前为 67 毫秒*[2]。*

![img](https://miro.medium.com/max/2000/0*wSL0WPiwIjnfEr06)

# What about Slivers?

如何防止安装新的`Element`与重用现有的？

## 相同类型的小部件

每当您更改列表中项目的顺序并且这些项目属于相同类型时，这些项目将被重用：

```
Widget build(BuildContext context) {
  return Directionality(
    textDirection: TextDirection.ltr,
    child: GestureDetector(
      onTap: () {
        setState(() {
          value = !value;
        });
      },
      child: ListView(
        children: <Widget>[
          value ? Placeholder(color: Colors.red) : Placeholder(),
          !value ? Placeholder(color: Colors.red) : Placeholder(),
        ],
      ),
    ),
  );
}
```

![img](https://miro.medium.com/max/60/1*3RmRSV6YYmmWBGBGed2eog.png?q=20)

![img](https://miro.medium.com/max/700/1*3RmRSV6YYmmWBGBGed2eog.png)

无需额外安装

## 不同类型的小部件

当您有不同类型的小部件并更改顺序时，将重新创建和安装`Elements`后面的小部件`Widgets`：

```
Widget build(BuildContext context) {
  return Directionality(
    textDirection: TextDirection.ltr,
    child: GestureDetector(
      onTap: () {
        setState(() {
          value = !value;
        });
      },
      child: ListView(
        children: <Widget>[
          value
              ? Placeholder(color: Colors.red)
              : Container(height: 100),
          !value
              ? Placeholder(color: Colors.red)
              : Container(height: 100),
        ],
      ),
    ),
  );
}
```

![img](https://miro.medium.com/max/2000/1*NPrr2PNau3PMDUCbrNQcFQ.png)

不幸的是，使用`LocalKeys`不会解决这个问题。使用全局键可以解决这个问题，但正如我之前提到的，滥用`GlobalKeys`可能会导致其他问题。

*谢谢* 

[*博美*](https://medium.com/u/f55c86c87a86?source=post_page-----3ef15c90f607--------------------------------)

 *指出这一点！*



# 结论

在大多数情况下，Flutter 的性能已经足够好，不需要微优化。另一方面，在某些情况下，我们需要做更多的工作才能让我们的应用程序以 60 FPS 的速度运行。

使用的缺点`ValueKeys`是它们`GlobalKey`会使我们的代码膨胀，如果我们复制它们，我们可能会出现一些错误。

负责任地使用 Keys 可以帮助您将应用程序的性能带到您想要的地方。

源代码可以在[这里](https://gist.github.com/tomaszpolanski/d0bbc49b6e3fb9faafecff4c4b20869f)找到。

如果您想了解有关 Elements 的更多信息，请查看 

[诺伯特](https://medium.com/u/aca5101434f?source=post_page-----3ef15c90f607--------------------------------)

的[文章](https://medium.com/flutter-community/flutter-what-are-widgets-renderobjects-and-elements-630a57d05208)。



[1] 为了简单起见，我从日志中删除了内部创建的元素的输出

[2] 这些测量是在模拟器上的调试版本上完成的——在发布版本中，在设备上，这些测量会小得多
