---
title: "Aoto_size_text"
date: 2021-09-02T13:42:42+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

# auto_size_text

Flutter 小部件可自动调整文本大小以完美适应其边界。

- 自述文件
- [变更日志](https://pub.dev/packages/auto_size_text/changelog)
- [例子](https://pub.dev/packages/auto_size_text/example)
- [安装](https://pub.dev/packages/auto_size_text/install)
- [版本](https://pub.dev/packages/auto_size_text/versions)
- [分数](https://pub.dev/packages/auto_size_text/score)

# ![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/logo.svg?sanitize=true) 

[![特拉维斯](https://luckly007.oss-cn-beijing.aliyuncs.com/image/master.svg)](https://travis-ci.com/leisim/auto_size_text) [![编解码器](https://luckly007.oss-cn-beijing.aliyuncs.com/image/badge.svg)](https://codecov.io/gh/leisim/auto_size_text) [![版本](https://luckly007.oss-cn-beijing.aliyuncs.com/image/auto_size_text.svg)](https://pub.dev/packages/auto_size_text) ![GitHub 许可证](https://luckly007.oss-cn-beijing.aliyuncs.com/image/license-MIT-blue.svg)

Flutter 小部件可自动调整文本大小以完美适应其边界。

**展示一些 ❤️ 并为 repo 加注星标以支持该项目**

### 资源：

- [文档](https://pub.dev/documentation/auto_size_text/latest/auto_size_text/AutoSizeText-class.html)
- [酒吧套餐](https://pub.dev/packages/auto_size_text)
- [GitHub 存储库](https://github.com/leisim/auto_size_text)
- [在线演示](https://appetize.io/app/w352kxbnz51c6pfvxrdvxcb3xw?device=nexus5&scale=100&orientation=landscape&osVersion=8.1&deviceColor=black)

还可以查看速度极快的键值存储[hive](https://github.com/leisim/hive)。

![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/maxlines.gif)

## 内容

- 用法
  - [最大行数](https://pub.dev/packages/auto_size_text#maxlines)
  - [minFontSize & maxFontSize](https://pub.dev/packages/auto_size_text#minfontsize--maxfontsize)
  - [团体](https://pub.dev/packages/auto_size_text#group)
  - [步进粒度](https://pub.dev/packages/auto_size_text#stepgranularity)
  - [预设字体大小](https://pub.dev/packages/auto_size_text#presetfontsizes)
  - [溢出替换](https://pub.dev/packages/auto_size_text#overflowreplacement)
- [富文本](https://pub.dev/packages/auto_size_text#rich-text)
- [参数](https://pub.dev/packages/auto_size_text#parameters)
- [表现](https://pub.dev/packages/auto_size_text#performance)
- 故障排除
  - [缺少边界](https://pub.dev/packages/auto_size_text#missing-bounds)
  - [MinFontSize 太大](https://pub.dev/packages/auto_size_text#minfontsize-too-large)

## 用法

`AutoSizeText`行为与`Text`. 唯一的区别是它会调整文本大小以适应其边界。

```dart
AutoSizeText(
  'The text to display',
  style: TextStyle(fontSize: 20),
  maxLines: 2,
)
```

**注意：** `AutoSizeText`需要有界约束来调整文本大小。更多信息[在这里](https://pub.dev/packages/auto_size_text#troubleshooting)。

### 最大行数

该`maxLines`参数的工作方式与您对`Text`小部件的使用方式相同。如果没有`maxLines`指定参数，则`AutoSizeText`仅根据可用的宽度和高度来适应文本。

```dart
AutoSizeText(
  'A really long String',
  style: TextStyle(fontSize: 30),
  maxLines: 2,
)
```

*上面的示例*

### minFontSize & maxFontSize 

以`AutoSizeText`开头`TextStyle.fontSize`。它测量生成的文本并重新调整它以适应其债券。但是，您可以设置结果字体大小的允许范围。

随着`minFontSize`您可以指定最小的字体大小。如果文本仍然不适合，将按照 处理`overflow`。默认`minFontSize`值为`12`.

`maxFontSize`设置最大可能的字体大小。如果`TextStyle`继承字体大小并且您想限制它，这很有用。

```dart
AutoSizeText(
  'A really long String',
  style: TextStyle(fontSize: 30),
  minFontSize: 18,
  maxLines: 4,
  overflow: TextOverflow.ellipsis,
)
```

![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/minfontsize.gif)

### 组

您可以同步多个`AutoSizeText`. 他们将符合他们的界限，并且`AutoSizeText`同一组中的所有人都具有相同的大小。这意味着他们将字体大小调整为具有最小有效字体大小的组成员。

**注意：**如果 a`AutoSizeText`由于诸如 之类的限制而无法调整`minFontSize`，则它不会与其他组成员具有相同的大小。

的实例`AutoSizeGroup`代表一个组。将此实例传递给`AutoSizeText`您要添加到该组的所有人。如果不再需要该组，则不必关心处置该组。

**重要提示：**请不要传递`AutoSizeGroup`每个构建的新实例。换句话说，将`AutoSizeGroup`实例保存在`StatefulWidget`.

```dart
var myGroup = AutoSizeGroup();

AutoSizeText(
  'Text 1',
  group: myGroup,
);

AutoSizeText(
  'Text 2',
  group: myGroup,
);
```

![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/group.gif)

### 步骤粒度

该`AutoSizeText`会尝试每个字体的大小，开始`TextStyle.fontSize`直至其边界内的文本适合。
`stepGranularity`指定每一步减少多少字体大小。通常，该值不应低于`1`最佳性能。

```dart
AutoSizeText(
  'A really long String',
  style: TextStyle(fontSize: 40),
  minFontSize: 10,
  stepGranularity: 10,
  maxLines: 4,
  overflow: TextOverflow.ellipsis,
)
```

![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/stepgranularity.gif)

### 预设字体大小

如果您只想允许特定的字体大小，您可以使用`presetFontSizes`. 如果`presetFontSizes`设置，`minFontSize`，`maxFontSize`和`stepGranularity`将被忽略。

```dart
AutoSizeText(
  'A really long String',
  presetFontSizes: [40, 20, 14],
  maxLines: 4,
)
```

![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/presetfontsizes.gif)

### 溢出替换

如果文本溢出并且不符合其边界，则会显示此小部件。这对于防止文本太小而无法阅读很有用。

```dart
AutoSizeText(
  'A String tool long to display without extreme scaling or overflow.',
  maxLines: 1,
  overflowReplacement: Text('Sorry String too long'),
)
```

![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/overflowreplacement.gif)

## 富文本

您还可以将富文本（如不同的文本样式或链接）与`AutoSizeText`. 只需使用`AutoSizeText.rich()`构造函数（它的工作原理与`Text.rich()`构造函数完全相同）。

你必须要知道的唯一的事情是字体大小的计算工作原理如下：`fontSize`在`style` 参数`AutoSizeText`（或继承`fontSize`，如果没有设置）作为参考。

例如：

```dart
AutoSizeText.rich(
  TextSpan(text: 'A really long String'),
  style: TextStyle(fontSize: 20),
  minFontSize: 5,
)
```

文本将至少是其原始大小的 1/4 (5 / 20 = 1/4)。
但这并不意味着所有的`TextSpan`s 都至少有 font size `5`。

![img](https://raw.githubusercontent.com/leisim/auto_size_text/master/.github/art/maxlines_rich.gif)

## **关于flutter - 时间轴 View ，日 View ，周 View ，月 View 并添加事件日历 flutter 库**

***\*最佳答案\**https://pub.dev/packages/flutter_week_view(如果有人要查看一天/一周的 View )，或者了解如何自己构建 View 。**

