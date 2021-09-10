---
title: "《Flutter中key的作用》"
subtitle: ""
date: 2021-07-26T22:52:25+08:00
lastmod: 2021-07-26T22:52:25+08:00
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

## key的定义

Key Class官方介绍：

> A [Key] is an identifier for [Widget]s, [Element]s and [SemanticsNode]s.
>
> A new widget will only be used to update an existing element if its key is
>
> the same as the key of the current widget associated with the element.
>
> {@youtube 560 315 https://www.youtube.com/watch?v=kn0EOS-ZiIc}
>
> Keys must be unique amongst the [Element]s with the same parent.
>
> Subclasses of [Key] should either subclass [LocalKey] or [GlobalKey].

翻译过来：

> 一个Key是Widget，Element以及SemanticsNode的标识。
>
> 一个新widget将仅用来更新一个已存在的element假如它的key和当前widget关联的元素一致。
>
> 官方介绍视频 https://www.youtube.com/watch?v=kn0EOS-ZiIc
>
> 在有着相同父节点的element中，Key必须是唯一的。
>
> Key的子类要么是LocalKey，要么是GlobalKey。

Key 官方介绍：

> Controls how one widget replaces another widget in the tree.
>
> If the [runtimeType](https://api.flutter.dev/flutter/widgets/Widget/dart-core/Object/runtimeType.html) and [key](https://api.flutter.dev/flutter/widgets/Widget/widgets/Widget/key.html) properties of the two widgets are [operator==](https://api.flutter.dev/flutter/widgets/Widget/widgets/Widget/operator_equals.html), respectively, then the new widget replaces the old widget by updating the underlying element (i.e., by calling [Element.update](https://api.flutter.dev/flutter/widgets/Widget/widgets/Element/update.html)with the new widget). Otherwise, the old element is removed from the tree, the new widget is inflated into an element, and the new element is inserted into the tree.
>
> In addition, using a [GlobalKey](https://api.flutter.dev/flutter/widgets/Widget/widgets/GlobalKey-class.html) as the widget’s [key](https://api.flutter.dev/flutter/widgets/Widget/widgets/Widget/key.html) allows the element to be moved around the tree (changing parent) without losing state. When a new widget is found (its key and type do not match a previous widget in the same location), but there was a widget with that same global key elsewhere in the tree in the previous frame, then that widget’s element is moved to the new location.
>
> Generally, a widget that is the only child of another widget does not need an explicit key.

翻译过来：

> 控制一个小部件如何替换树中的另一个小部件。
>
> 如果两个widget的[runtimeType](https://api.flutter.dev/flutter/dart-core/Object/runtimeType.html)和[key](https://api.flutter.dev/flutter/widgets/Widget/key.html)属性分别是相等的([==](https://api.flutter.dev/flutter/widgets/Widget/operator_equals.html))，则新widget通过更新基础element(即，通过使用新的widget调用[Element.update](https://api.flutter.dev/flutter/widgets/Element/update.html))来替换旧widget。否则，将从树中删除旧element，将新widget放大为一个element，然后将新element插入到树中。
>
> 另外，使用[GlobalKey](https://api.flutter.dev/flutter/widgets/GlobalKey-class.html)作为窗口小部件的[key](https://api.flutter.dev/flutter/widgets/Widget/key.html)允许该element在树上移动(更改父级)而不会丢失状态。当找到新的widget(其键和类型与相同位置的先前widget不匹配)，但是在前一帧的树中其他位置有一个具有相同全局键的widget时，该widget的element将移至新位置。
>
> 通常，作为另一个widget的唯一child的widget不需要显式key。

## Key的作用

大多数时候并不需要使用key。

当需要在一个**StatefulWidget**集合中进行添加、删除、重排序等操作时，才是key登场的时候。

### 无状态组件

下面这段代码在一个Row中展示了两个彩色方片(StatelessContainer)，当点击按钮时，会交换两个方片的位置：

[![img_key_screen](https://luckly007.oss-cn-beijing.aliyuncs.com/img/img_key.png)](https://blog.wangruofeng007.com/images/flutter_key/img_key_screen.png)

代码如下

```
import 'dart:math';
import 'package:flutter/material.dart';

class StatelessContainer extends StatelessWidget {
  final Color color = Color.fromRGBO(
      Random().nextInt(256), Random().nextInt(256), Random().nextInt(256), 1);

  @override
  Widget build(BuildContext context) {
    return Container(
      width: 100,
      height: 100,
      color: color,
    );
  }
}

class Screen extends StatefulWidget {
  @override
  _ScreenState createState() => _ScreenState();
}

class _ScreenState extends State<Screen> {
  List<Widget> widgets = [
    StatelessContainer(),
    StatelessContainer(),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Row(
          mainAxisAlignment: MainAxisAlignment.center,
          children: widgets,
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: switchWidget,
        child: Icon(Icons.undo),
      ),
    );
  }

  switchWidget() {
    widgets.insert(0, widgets.removeAt(1));
    setState(() {});
  }
}
```

### 有状态组件

有状态组件的状态信息(如颜色)通常是存储在state中的，而state是存储在element树中的。

那么Key到底应该用到哪呢？
我们再来一个例子，我们把色块用Padding包装一下。运行之后会发现，色块并没有交换，而是以随机的形式在变换颜色。为什么呢？

```
import 'dart:math';
import 'package:flutter/material.dart';

class Screen extends StatefulWidget {
  Screen({Key key}) : super(key: key);

  @override
  _ScreenState createState() => _ScreenState();
}

class _ScreenState extends State<Screen> {
  List<Widget> widgets = [
    Padding(
      padding: const EdgeInsets.all(8.0),
      child: StatefulContainer(key: UniqueKey()),
    ),
    Padding(
      padding: const EdgeInsets.all(8.0),
      child: StatefulContainer(key: UniqueKey()),
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Row(
          mainAxisAlignment: MainAxisAlignment.center,
          children: widgets,
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: switchWidget,
        child: Icon(Icons.undo),
      ),
    );
  }

  switchWidget() {
    widgets.insert(0, widgets.removeAt(1));
    setState(() {});
    print('${widgets[0]}, ${widgets[1]}');
  }
}

class StatefulContainer extends StatefulWidget {
  StatefulContainer({Key key}) : super(key: key);

  @override
  _StatefulContainerState createState() => _StatefulContainerState();
}

class _StatefulContainerState extends State<StatefulContainer> {
  final Color color = Color.fromRGBO(
      Random().nextInt(256), Random().nextInt(256), Random().nextInt(256), 1);

  @override
  Widget build(BuildContext context) {
    return Container(
      color: color,
      width: 100,
      height: 100,
    );
  }
}
```

结合我们上面的理论，我们分析一下这次的Widget Tree 和 Element Tree，当我们交换元素后，Flutter element-to-widget matching algorithm,(元素-组件匹配算法)，开始进行对比，算法每次只对比一层，即Padding这一层。显然，Padding并没有发生本质的变化。

于是开始进行第二层对比，在对比时Flutter发现元素与组件的Key并不匹配，于是，把它设置成不可用状态，但是这里所使用的Key只是本地Key(Local Key)，Flutter并不能找到另一层里面的Key(即另外一个Padding Widget中的Key)所以，Flutter就创建了一个新的Widget，而这个Widget的颜色就成了我们看到的『随机色』。

通过上面的示例，我们能明显的看出，我们的Key要设置到组件树的 **顶层**，而这一层在改变时，才能复用或者更新状态。

修正版本：

```
import 'dart:math';

import 'package:flutter/material.dart';

class Screen extends StatefulWidget {
  Screen({Key key}) : super(key: key);

  @override
  _ScreenState createState() => _ScreenState();
}

class _ScreenState extends State<Screen> {
  List<Widget> widgets = [
    Padding(
      key: UniqueKey(),
      padding: const EdgeInsets.all(8.0),
      child: StatefulContainer(),
    ),
    Padding(
      key: UniqueKey(),
      padding: const EdgeInsets.all(8.0),
      child: StatefulContainer(),
    ),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Row(
          mainAxisAlignment: MainAxisAlignment.center,
          children: widgets,
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: switchWidget,
        child: Icon(Icons.undo),
      ),
    );
  }

  switchWidget() {
    widgets.insert(0, widgets.removeAt(1));
    setState(() {});
    print('${widgets[0]}, ${widgets[1]}');
  }
}

class StatelessContainer extends StatelessWidget {
  final Color color = Color.fromRGBO(
      Random().nextInt(256), Random().nextInt(256), Random().nextInt(256), 1);

  @override
  Widget build(BuildContext context) {
    return Container(
      color: color,
      width: 100,
      height: 100,
    );
  }
}

class StatefulContainer extends StatefulWidget {
  StatefulContainer({Key key}) : super(key: key);

  @override
  _StatefulContainerState createState() => _StatefulContainerState();
}

class _StatefulContainerState extends State<StatefulContainer> {
  final Color color = Color.fromRGBO(
      Random().nextInt(256), Random().nextInt(256), Random().nextInt(256), 1);

  @override
  Widget build(BuildContext context) {
    return Container(
      color: color,
      width: 100,
      height: 100,
    );
  }
}
```

## Key的分类

[![img_key](https://luckly007.oss-cn-beijing.aliyuncs.com/img/img_key.png)](https://blog.wangruofeng007.com/images/flutter_key/img_key.png)

- ValueKey:以一个值为key。
- ObjectKey:以一个对象为key。
- UniqueKey:生成唯一的随机数作为key。
- PageStorageKey:专用于存储页面滚动位置的key。
- GlobalKey:见后文。

## 何时使用key

### ValueKey

如果您有一个 Todo List 应用程序，它将会记录你需要完成的事情。我们假设每个 Todo 事情都各不相同，而你想要对每个 Todo 进行滑动删除操作。

这时候就需要使用 ValueKey！

```
return TodoItem(
    key: ValueKey(todo.task),
    todo: todo,
    onDismissed: (direction){
        _removeTodo(context, todo);
    },
);
```

### ObjectKey

如果你有一个生日应用，它可以记录某个人的生日，并用列表显示出来，同样的还是需要有一个滑动删除操作。

我们知道人名可能会重复，这时候你无法保证给 Key 的值每次都会不同。但是，当人名和生日组合起来的 Object 将具有唯一性。

这时候你需要使用 ObjectKey！

### UniqueKey

如果组合的 Object 都无法满足唯一性的时候，你想要确保每一个 Key 都具有唯一性。那么，你可以使用 UniqueKey。它将会通过该对象生成一个具有唯一性的 hash 码。

不过这样做，每次 Widget 被构建时都会去重新生成一个新的 UniqueKey，失去了一致性。也就是说你的小部件还是会改变。(还不如不用😂)

### PageStorageKey

当你有一个滑动列表，你通过某一个 Item 跳转到了一个新的页面，当你返回之前的列表页面时，你发现滑动的距离回到了顶部。这时候，给 Sliver 一个 PageStorageKey！它将能够保持 Sliver 的滚动状态。

### GlobalKey

每个globalkey都是一个在整个应用内唯一的key。

globalkey相对而言是比较昂贵的，如果你并不需要globalkey的某些特性，那么可以考虑使用Key、ValueKey、ObjectKey或UniqueKey。

#### 用途1

允许widget在应用程序中的任何位置更改其parent而不丢失其状态。应用场景：在两个不同的屏幕上显示相同的widget，并保持状态相同。

#### 用途2

GlobalKey 能够跨 Widget 访问状态。 在这里我们有一个 Switcher 小部件，它可以通过 changeState 改变它的状态。

```
class SwitcherScreenState extends State<SwitcherScreen> {
  bool isActive = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Switch.adaptive(
            value: isActive,
            onChanged: (bool currentStatus) {
              isActive = currentStatus;
              setState(() {});
            }),
      ),
    );
  }

  changeState() {
    isActive = !isActive;
    setState(() {});
  }
}
```

但是我们想要在外部改变该状态，这时候就需要使用 GlobalKey。

```
class _ScreenState extends State<Screen> {
  final GlobalKey<SwitcherScreenState> key = GlobalKey<SwitcherScreenState>();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SwitcherScreen(
        key: key,
      ),
      floatingActionButton: FloatingActionButton(onPressed: () {
        key.currentState.changeState();
      }),
    );
  }
}
```

这里我们通过定义了一个 GlobalKey 并传递给 SwitcherScreen。然后我们便可以通过这个 key 拿到它所绑定的 SwitcherState 并在外部调用 changeState 改变状态了。

## 参考资料

- [何时使用密钥 - Flutter小部件 101 第四集](https://www.youtube.com/watch?v=kn0EOS-ZiIc&feature=youtu.be)
- [widgets-intro#keys](https://flutter.dev/docs/development/ui/widgets-intro#keys)
- [Flutter | 深入浅出Key](https://juejin.im/post/5ca2152f6fb9a05e1a7a9a26)
- [Flutter中的Key和GlobalKey](https://blog.csdn.net/al4fun/article/details/95854045)