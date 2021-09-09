---
title: "如何使我们的UI代码更具可读性"
date: 2021-09-09T09:05:09+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

代码*越多*，保持可读性的*责任就越大*。从长远来看，保持代码可读性使其更易于维护。让我们看一些关于如何使我们的 UI 代码更具可读性的快速提示。

## 问题 #1 - “填充、填充、填充”

我们应用程序中的大多数布局都基于垂直或水平布局的内容。这意味着很多时候，我们使用`Column`或`Row`小部件。

由于将小部件放在彼此的正下方或旁边并不总是看起来很好，我们希望它们之间有一些边距。在两个小部件之间留出边距的一种明显方法是用小部件包装其中之一`Padding`。

考虑以下示例：

```
return Column(
  children: [
    Text('First line of text.'),
    const Padding(
      padding: EdgeInsets.only(top: 8),
      child: Text('Second line of text.'),
    ),
    const Padding(
      padding: EdgeInsets.only(top: 8),
      child: Text('Third line of text.'),
    ),
  ],
);
```

我们`Text`在 a 中有三个小部件`Column`，每个小部件`8.0`之间都有垂直边距。

### 问题：隐藏的小部件

`Padding`到处使用小部件的问题在于它们开始模糊我们 UI 代码的“业务逻辑”。它们以增加缩进级别和行数的形式增加了一些视觉混乱。

我们希望*尽可能多地弹出实际的小部件*。每个额外的缩进级别都很重要。如果我们可以减少沿途的行数，那也很棒。

### 解决方案：使用 SizedBoxes

为了解决“隐藏小部件问题”，我们可以`Padding`用`SizedBox`小部件替换所有的s 。使用`SizedBoxes`代替`Padding`s 可以减少缩进级别和行数：

```
return Column(
  children: [
    Text('First line of text.'),
    const SizedBox(height: 8),
    Text('Second line of text.'),
    const SizedBox(height: 8),
    Text('Third line of text.'),
  ],
);
```

同样的方法也可以用于`Row`s。由于`Row`s 是水平放置它们的孩子，我们可以使用 上的`width`属性`SizedBox`来设置水平边距。

## 问题 #2 - 过度附加的回调

点击或触摸可以说是用户与我们的应用程序交互的最常见方式。

为了允许用户点击我们应用程序中的某个地方，我们可以使用`GestureDetector`小部件。使用时`GestureDetectors`，我们将原始小部件包装在其中并指定对`onTap`构造函数参数的回调。

考虑从我的[inKino 应用程序中](https://github.com/roughike/inKino)获取的以下示例：



关于问题2的其他想法：

```
Iterable<widget> buildChildren() sync* {  
  if (event.storyline != null) {
   yield _buildStoryline();
  }

  if (event.actors.isNotEmpty) {
    yield ListView(...);
  }
}

Widget build() {  
  return Row(children: buildChildren().toList());
}  
```

Dart 语言不允许`&&`,`||`与其他类型一起工作，使构建 UI 变得复杂。

```
// ...
final List<Event> events;

@override
Widget build(BuildContext context) {
  return GridView.builder(
    // ...
    itemBuilder: (_, int index) {
      final event = events[index];

      return GestureDetector(
        onTap: () {
          // ...
          Navigator.push(
            context,
            MaterialPageRoute(
              builder: (_) => EventDetailsPage(event),
            ),
          );
        },
        child: EventGridItem(event),
      );
    },
  );
}
```

inKino 应用程序有一个电影海报网格。当用户点击其中之一时，他们应该被带到电影详细信息页面。

### 问题：用逻辑乱扔 UI 代码

我们的构建方法应该只包含与构建我们的应用程序 UI 相关的最少代码。`onTap`回调中包含的逻辑与构建 UI 完全无关。它给我们的构建方法增加了不必要的噪音。

在这种情况下，我们可以非常快速地确定`Navigator.push`推送了一条新路线，并且确实`EventDetailsPage`如此——因此点击网格项目会打开一个详细信息页面。但是，如果`onTap`回调涉及更多，则可能需要更深入的阅读才能理解。

### 解决方案：将逻辑提取到私有方法中

通过将`onTap`回调提取到一个命名良好的私有方法中，可以非常巧妙地解决这个问题。在我们的例子中，我们创建了一个名为 的新方法`_openEventDetails`：

```
final List<Event> events;

// ...
void _openEventDetails(BuildContext context, Event event) {
  Navigator.push(
    context,
    MaterialPageRoute(
      builder: (_) => EventDetailsPage(event),
    ),
  );
}

@override
Widget build(BuildContext context) {
  return GridView.builder(
    // ...
    itemBuilder: (_, int index) {
      final event = events[index];

      return GestureDetector(
        // ...
        onTap: () => _openEventDetails(context, event),
        child: EventGridItem(event),
      );
    },
  );
}
```

这要好得多。

由于`onTap`回调现在被提取到一个命名良好的私有方法中，我们不必再通读整个代码。现在只需看一眼就很容易理解调用回调时会发生什么。

我们还在我们的构建方法中保存了很多宝贵的代码行，专注于阅读 UI 相关的代码。

## 问题 #3 - 如果到处都是

有时，我们`Columns`(或`Rows`) 的所有子级并不意味着始终可见。例如，如果一部电影由于某种原因缺少故事情节细节，那么`Text`在 UI 中显示一个空的小部件是没有意义的。

有条件地将子项添加到 a `Column`(or `Row`)的常见习语如下所示：

```
class EventDetailsPage extends StatelessWidget {
  EventDetailsPage(this.event);
  final Event event;

  @override
  Widget build(BuildContext context) {
    final children = <Widget>[
      _HeaderWidget(),
    ];

    // ...
    if (event.storyline != null) {
      children.add(StorylineWidget(event.storyline));
    }

    // ...
    if (event.actors.isNotEmpty) {
      children.add(ActorList(event.actors));
    }

    return Scaffold(
      // ...
      body: Column(children: children),
    );
  }
}
```

有条件地将项添加到 a 的要点`Column`非常简单：我们初始化一个本地小部件列表，如果满足某些条件，我们将向其中添加必要的子项。最后，我们在`children`我们的`Column`.

在我的例子中，也就是上面的例子，Finnkino API 并不总是返回所有电影的故事情节或演员。

### 问题：如果无处不在

虽然这有效，但那些 if 语句会很快变老。

尽管它们非常易于理解和直接，但它们在我们的构建方法中占用了不必要的垂直空间。特别是拥有三个或更多开始变得非常麻烦。

### 解决方案：实用方法

为了解决这个问题，我们可以创建一个全局实用程序方法来为我们添加条件小部件。以下是在主要 Flutter 框架代码中也使用的模式：

widget_utils.dart

```
void addIfNonNull(Widget child, List children) {
  if (child != null) {
    children.add(child);
  }
}
```

我们没有复制有条件地将子级添加到小部件列表的逻辑，而是为其创建一个全局实用程序方法。

定义后，我们将导入文件并开始使用全局方法：

```
import 'widget_utils.dart';

class EventDetailsPage extends StatelessWidget {
  EventDetailsPage(this.event);
  final Event event;

  Widget _buildStoryline() =>
      event.storyline != null ? StorylineWidget(event.storyline) : null;

  Widget _buildActorList() =>
      event.actors.isNotEmpty ? ActorList(event.actors) : null;

  @override
  Widget build(BuildContext context) {
    final children = <Widget>[
      _HeaderWidget(),
    ];

    // ...
    addIfNonNull(_buildStoryline(), children);
    addIfNonNull(_buildActorList(), children);

    return Scaffold(
      // ...
      body: Column(children: children),
    );
  }
}
```

我们在这里所做的是，现在我们的`_buildMyWidget()`方法返回小部件或`null`，这取决于我们的条件是否为真。这允许我们在我们的构建方法中节省一些垂直空间，特别是如果我们有很多有条件添加的小部件。

## 问题 #4 - 支架地狱

最好把最好的留到最后。

这可能是我们布局代码中最普遍的问题之一。一个常见的抱怨是 Flutter UI 代码可以达到一些疯狂的缩进级别，从而产生许多括号。

考虑以下示例：

```
// ...
@override
Widget build(BuildContext context) {
  final backgroundColor =
      useAlternateBackground ? const Color(0xFFF5F5F5) : Colors.white;

  return Material(
    color: backgroundColor,
    child: InkWell(
      onTap: () => _navigateToEventDetails(context),
      child: Padding(
        padding: const EdgeInsets.symmetric(/* ... */),
        child: Row(
          children: [
            Column(
              children: [
                Text(
                  hoursAndMins.format(show.start),
                  style: const TextStyle(/* ... */),
                ),
                Text(
                  hoursAndMins.format(show.end),
                  style: const TextStyle(/* ... */),
                ),
              ],
            ),
            const SizedBox(width: 20.0),
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: [
                  Text(
                    show.title,
                    style: const TextStyle(/* ... */),
                  ),
                  const SizedBox(height: 4.0),
                  Text(show.theaterAndAuditorium),
                  const SizedBox(height: 8.0),
                  Container(
                    // ...
                    // ...
                    child: Text(show.presentationMethod),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    ),
  );
}
```

上面的示例来自[我的名为 inKino 的电影应用程序](https://github.com/roughike/inKino)，其中包含为电影放映时间构建列表图块的代码。我故意让它看起来很丑。很多内容都被编辑了——相信我，当我说完整的例子会很有意义。

本质上，这是用于构建这些坏男孩的代码：

![inKino 应用程序中的放映时间列表磁贴小部件。](https://luckly007.oss-cn-beijing.aliyuncs.com/image/showtime_list_tiles.png)

如果您正在使用移动设备阅读本文，我很抱歉。即使在更大的屏幕上，上面的代码示例也不好看。为什么？我很确定你们大多数人已经知道了。

### 问题：你甚至是 Lisp 吗？

这种古老的编程语言称为 Lisp，它的语法使您可以使用许多括号。我已经多次看到 Flutter 的 UI 标记与 Lisp 相比，老实说，我看到了相似之处。

令人惊讶的是，以前没有人这样做过，所以就这样吧。

《如何用Flutter拯救公主》：

![用 Flutter 拯救公主](https://luckly007.oss-cn-beijing.aliyuncs.com/image/rescuing_princess_with_flutter.png)

上图[由 Toggl & Matt Virkus](https://toggl.com/programming-princess/)修改自[“如何用 8 种编程语言拯救公主”](https://toggl.com/programming-princess/)。他们的漫画很有趣——去看看吧。

虽然上面的代码有效，但看起来并不那么漂亮。缩进级别越来越深，有很多垂直混乱，括号，并且很难跟踪正在发生的事情和发生的位置。

看看这里的结束括号：

```
                      ),
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    ),
  );
}
```

由于嵌套很深，即使有一个好的 IDE，也很难向我们的布局添加新元素。更不用说，实际阅读 UI 代码。

### 解决方案：将不同的 UI 部分重构为单独的小部件

我们的列表磁贴由两个不同的部分组成：左侧和右侧。

左侧包含有关电影开始和结束时间的信息。右侧有诸如电影名称、剧院以及它是 2D 还是 3D 电影等信息。为了使代码更具可读性，我们首先将其分解为两个不同的小部件，称为`_LeftPart`和`_RightPart`。

由于呈现方法小部件会引入相当多的垂直混乱和深度嵌套，因此我们将其分解为一个名为 的单独小部件`_PresentationMethod`。 **注意：**不要将您的构建方法拆分为单独的方法——这是一种性能反模式，值得专门写一篇文章。

```
class ShowListTile extends StatelessWidget {
  ShowListTile(this.show);

  // ...
  final Show show;

  @override
  Widget build(BuildContext context) {
    final backgroundColor =
        useAlternateBackground ? const Color(0xFFF5F5F5) : Colors.white;

    return Material(
      color: backgroundColor,
      child: InkWell(
        onTap: () => _navigateToEventDetails(context),
        child: Padding(
          padding: const EdgeInsets.symmetric(/* ... */),
          child: Row(
            children: [
              _LeftPart(show),
              const SizedBox(width: 20.0),
              _RightPart(show),
            ],
          ),
        ),
      ),
    );
  }
}

class _LeftPart extends StatelessWidget {
  _LeftPart(this.show);
  final Show show;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text(
          hoursAndMins.format(show.start),
          style: const TextStyle(/* ... */),
        ),
        Text(
          hoursAndMins.format(show.end),
          style: const TextStyle(/* ... */),
        ),
      ],
    );
  }
}

class _RightPart extends StatelessWidget {
  _RightPart(this.show);
  final Show show;

  @override
  Widget build(BuildContext context) {
    return Expanded(
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Text(
            show.title,
            style: const TextStyle(/* ... */),
          ),
          const SizedBox(height: 4.0),
          Text(show.theaterAndAuditorium),
          const SizedBox(height: 8.0),
          // ...
          // ...
          _PresentationMethodChip(show),
        ],
      ),
    );
  }
}

class _PresentationMethodChip extends StatelessWidget {
  _PresentationMethodChip(this.show);
  final Show show;

  @override
  Widget build(BuildContext context) {
    return Container(
      // ...
      // ...
      child: Text(
        show.presentationMethod,
        style: const TextStyle(/* ... */),
      ),
    );
  }
}
```

通过这些更改，缩进级别现在比以前少了一半以上。现在可以轻松浏览 UI 代码并查看正在发生的情况和位置。

## 奖励 - 发明您自己的格式样式

我不认为这是与上述问题类似的问题，但这仍然是非常重要的事情。为什么？让我们来看看。

为了说明这个问题，请看下面的代码示例：

```
@override
Widget build(BuildContext context) {
  return Column(
      children:[Row(children:
      [Text('Hello'),Text('World'),
        Text('!')])]);
}
```

这很奇怪，不是吗？当然不是你在一个好的代码库中看到的东西。

### 问题：不使用 dartfmt

上面的代码示例不遵守任何常见的 Dart 格式约定 - 似乎该代码的作者发明了自己的风格。这不好，因为阅读这样的代码需要一些额外的注意力——它不使用我们习惯的约定。

拥有普遍认同的代码风格是必不可少的。这使我们可以跳过习惯一些没人熟悉的奇怪格式样式的心理体操。

### 解决方案：只需使用 dartfmt

幸运的是，我们有一个名为*dartfmt*的官方格式化程序，它负责为我们进行格式化。此外，由于存在“格式化程序垄断”，我们可以停止争论哪个是最好的格式化程序，而是专注于我们的代码。

根据经验，在每个括号后面总是有逗号，并且运行 dartfmt 有很长的路要走：

```
return Column(
  children: [
    Row(
      children: [
        Text('Hello'),
        Text('World'),
      ],
    ),
    Text('!'),
  ],
);
```

好多了。格式化我们的代码是必须的 - 始终记住您的逗号并使用 dartfmt 格式化您的代码。
