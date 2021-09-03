---
title: "Flutter组件TabBar、TabBarView，TabController实现Tab标签菜单布局"
date: 2021-09-01T09:08:22+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## 一、TabBar 和 TabBarView

Tab 标签栏的基本组成是顶部的 Tab标题和内容区域，在 Flutter 中，Material 提供了 TabBar 和 TabBarView 两个 Widget



### 1、TabBar

`TabBar` 构造函数如下，其中只有 `tabs` 是必须的，接收一个 `List<Tab>` 列表，indicator 相关的都是标题的下划线相关的样式配置，而 label 则是文字的相关样式

```dart
  const TabBar({
    Key key,
    @required this.tabs,
    this.controller,
    this.isScrollable = false,
    this.indicatorColor,
    this.indicatorWeight = 2.0,
    this.indicatorPadding = EdgeInsets.zero,
    this.indicator,
    this.indicatorSize,
    this.labelColor,
    this.labelStyle,
    this.labelPadding,
    this.unselectedLabelColor,
    this.unselectedLabelStyle,
    this.dragStartBehavior = DragStartBehavior.start,
    this.onTap,
  })
```

如果你在页面上直接使用 `TabBar` 或者 TabBarView 可能会得到以下错误：

![29966-3zu2iy4v5yo.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/1916162396.png)

`TabBar` 定义中也说了：

> /// If a [TabController] is not provided, then there must be a
> /// [DefaultTabController] ancestor.

TabBar 和 TabBarView 都必须放在一个开发者提供的 TabController 或者 `DefaultTabController`（默认的 TabController）中

```dart
body: DefaultTabController(
  length: 9,
  child: Column(
    children: <Widget>[
      Container(
        color: Colors.pink,
        child: TabBar(
          labelColor: Colors.yellow,
          unselectedLabelColor: Colors.white,
          indicatorWeight: 1,
          isScrollable: true,
          // labelPadding: EdgeInsets.fromLTRB(10, 0, 0, 0),
          labelStyle: TextStyle(fontSize: 14),
          tabs: <Widget>[
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
            Tab(text: 'Tab1'),
          ],
        ),
      )
    ],
  ),
)
```

上面代码中，body 里面就是 DefaultTabController，然后将 `TabBar` 放在其中

最终的效果：

![87808-i296al8xp1k.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/3366519146.png)



## 2、TabBarView

有了 `TabBar` 只要在创建相对应数目的 TabBarView 即可，与 TabBar 相对应

`TabBarView` 构造相对简单，毕竟只是个特殊的容器：

```dart
  /// Creates a page view with one child per tab.
  ///
  /// The length of [children] must be the same as the [controller]'s length.
  const TabBarView({
    Key key,
    @required this.children,
    this.controller,
    this.physics,
    this.dragStartBehavior = DragStartBehavior.start,
  })
```

容器中其实可以放置任何我们想要放置的内容，比如创建一个 ListView：

```dart
class ListViewContnet extends StatelessWidget {
  const ListViewContnet({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ListView(
      children: <Widget>[
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
        ListTile(title: Text(TITLE)),
      ],
    );
  }
}
```

然后在上面的 `DefaultTabController` 中加上 `TabBarView`

使用 TabBarView 是有条件要求的，TabBarView 的父 Widget 必须知道宽高才能布局，但是一般我们在实际项目使用的时候又不会写死宽高，因此一般我都会在一个 `Expanded` 中使用 TabBarView。

比如上面示例中，TabBar 已经放在了 Column 中，因此 整个剩下的 Column 空间都是用来给 TabBarView 使用的：

```dart
Expanded(
  flex: 1,
  child: TabBarView(
    children: <Widget>[
      ListViewContnet(),
      ListViewContnet(),
      ListViewContnet(),
      ListViewContnet(),
      ListViewContnet(),
      ListViewContnet(),
      ListViewContnet(),
      ListViewContnet(),
      ListViewContnet(),
    ],
  ),
)
```

Expanded 设置 `flex:1` ，Column 空间剩下的都给 TabBarView 使用，最终效果：

![51008-a4ijhzkv5.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/2514608225.png)

完整代码在文章最后有链接。



## 二、tabController 实现的 Tab 菜单栏

上面虽然实现了一个 tab 菜单栏效果，但实际上我们无法控制，拿不到事件，比如在实际使用项目中，切换不同的 Tab 会重新请求数据，而上面的实现则不行

TabBar 的构造函数中，有一个 `controller` 的参数，定义如下：

```dart
  /// This widget's selection and animation state.
  ///
  /// If [TabController] is not provided, then the value of [DefaultTabController.of]
  /// will be used.
  final TabController controller;
```

这个和 TextInpute 的 InputeController 差不多一样的意思，通过一个 Controller 能够在一个 Widget class 中随意的挥霍和控制 TabController.

创建一个 `TabController` 非常简单：

```dart
    this._tabController = new TabController(vsync: this, length: 5);
    this._tabController.addListener(() {
      print(this._tabController.toString());
      print(this._tabController.index);
      print(this._tabController.length);
      print(this._tabController.previousIndex);
    });
```

`.addListenter` 可以对 TabController 增加监听，每次发生切换，都能够走到方法中

要使用 `TabController`，比如混入一个 `SingleTickerProviderStateMixin`

```dart
class _TabControllerDemoState extends State<TabControllerDemo>  with SingleTickerProviderStateMixin {
```

除此之外，使用方式和上面差不多，只不过需要在 `TabBar` 和 `TabBarView` 中都传入生成的 `controller`

```dart
title: TabBar(
controller: this._tabController,
tabs: <Widget>[
  Tab(text: '女装'),
  Tab(text: '男装'),
  Tab(text: '童装'),
  Tab(text: '夏装'),
  Tab(text: '冬装'),
],
)
body: TabBarView(
  controller: this._tabController,
  children: <Widget>[
    ListViewContnet(),
    ListViewContnet(),
    ListViewContnet(),
    ListViewContnet(),
    ListViewContnet(),
  ],
));
```



## 三、Appbar + TabBar 实现的布局

最开始提到了，TabBarView 必须有一个固定宽高，而一般为了撑满页面，我们会选择使用 Column 这个 Widget，通过 Expanded 实现宽度高度自适应

初次之外，还可以借助 `Scalfold` 实现，因为 Scalfold 本身就能够撑满整个屏幕，appbar 在上面，而 body 则是其他部分

根据这个布局，`TabBar` 可以放在 `Scalfold.appBar` 中，而 `TabBarView` 则是放在 `Scalfold.TabBarView` 中

Scalfold 本身还是可以嵌套第二个 Scalfol，因此对页面本身没有什么影响。

比如借助 Scalfold 和 `TabController` 实现的布局：

```dart
class TabControllerDemo extends StatefulWidget {
  TabControllerDemo({Key key}) : super(key: key);

  _TabControllerDemoState createState() => _TabControllerDemoState();
}

class _TabControllerDemoState extends State<TabControllerDemo>  with SingleTickerProviderStateMixin {
  TabController _tabController;

  @override
  void initState() {
    super.initState();
    this._tabController = new TabController(vsync: this, length: 5);
    this._tabController.addListener(() {
      print(this._tabController.toString());
      print(this._tabController.index);
      print(this._tabController.length);
      print(this._tabController.previousIndex);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          backgroundColor: Colors.black,
          title: TabBar(
            controller: this._tabController,
            tabs: <Widget>[
              Tab(text: '女装'),
              Tab(text: '男装'),
              Tab(text: '童装'),
              Tab(text: '夏装'),
              Tab(text: '冬装'),
            ],
          ),
        ),
        body: TabBarView(
          controller: this._tabController,
          children: <Widget>[
            ListViewContnet(),
            ListViewContnet(),
            ListViewContnet(),
            ListViewContnet(),
            ListViewContnet(),
          ],
        ));
  }
}
```

最终效果：

![18370-xywwawmwgq.png](https://luckly007.oss-cn-beijing.aliyuncs.com/image/3747763428.png)

不过因为 Matedial 的 AppBar 样式可以设置的不多，因此并不是很推荐放在 AppBar 中



## 四、完整代码



### 1、TabBar 和 TabBarView

https://github.com/postbird/FlutterHelloWorldDemo/blob/dev1/demo1/lib/bak/main.31-TabBar.dart



### 2、TabController

[https://github.com/postbird/FlutterHelloWorldDemo/blob/dev1/demo1/lib/bak/main.32-TabController%20%E5%AE%9E%E7%8E%B0Tab.dart](https://github.com/postbird/FlutterHelloWorldDemo/blob/dev1/demo1/lib/bak/main.32-TabController 实现Tab.dart)
