---
title: "在带有背景图像的Flutter中拉动以刷新"
date: 2021-09-29T09:37:13+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍在带有背景图像的Flutter中拉动以刷新

拉动刷新”是显示动态数据列表的移动应用程序的常见任务。今天我们将使用 pull_to_refresh 包实现 pull to refresh 并利用 Slivers 创建一个自定义的 Collapsible 标头

![刷新示例](https://luckly007.oss-cn-beijing.aliyuncs.com/img/example.fef7531.bfe2748b7b8d65802a9aa62a3401fa81.gif)

## 设置

我们将首先将 pull_to_refresh 包添加到我们的项目中

```yaml
pull_to_refresh: ^1.4.5
```

然后我们可以使用 a`MaterialApp`和一个`Home`小部件进行基本设置。

```dart
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(title: 'Flutter Demo', home: Home());
  }
}

class Home extends StatelessWidget {
  final RefreshController _refreshController = RefreshController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[700],
      body: Container(),
    );
  }
}
```

然后我们将创建一个函数，该函数返回我们将用于在列表中显示的小部件列表。我们将在彼此下方显示高度为 100 的圆角矩形。

```dart
 List<Widget> buildList() {
    return List.generate(
        15,
        (index) => Container(
              height: 100,
              margin: const EdgeInsets.symmetric(
                vertical: 10,
                horizontal: 15,
              ),
              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.circular(15),
              ),
            ));
  }
```

## 执行

现在我们可以继续添加 PullToRefresh 功能。我们将导入包然后创建我们的控制器作为最终变量。我们将使我们的脚手架主体成为一个 SmartRefresher 来接收我们的控制器。

```dart
import 'package:pull_to_refresh/pull_to_refresh.dart';

class Home extends StatelessWidget {
  final RefreshController _refreshController = RefreshController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[700],
      body: SmartRefresher(
        controller: _refreshController,
        enablePullDown: true,
        header: defaultHeader,
        onRefresh: () async {
          await Future.delayed(Duration(seconds: 1));
          _refreshController.refreshCompleted();
        },
         child: CustomScrollView(
          slivers: [
            SliverList(delegate: SliverChildListDelegate(buildList()))
          ],
        ),
      ),
    );
  }

  ...
}
```

上面我们启用了下拉功能，我们提供了随包提供的默认标头，并且在刷新时我们希望延迟，然后指示刷新已完成。我们还将我们的 List 设置在一个`CustomScrollView`. 因为我们知道我们正在添加一个可折叠的工具栏，所以我们知道将涉及到条子。仅此代码就可以让您在没有任何背景或可折叠应用栏的情况下刷新。

### 添加可折叠的 AppBar 和图像

要添加 RefreshBackground，我们将创建一个新的小部件，其中根子级是`SliverAppBar`.

```dart
class RefreshBackground extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return SliverAppBar(
      expandedHeight: 200.0,
      flexibleSpace: FlexibleSpaceBar(
          background: Image.network(
        "https://images.unsplash.com/photo-1541701494587-cb58502866ab?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=0c21b1ac3066ae4d354a3b2e0064c8be&auto=format&fit=crop&w=500&q=60",
        fit: BoxFit.cover,
      )),
    );
  }
}
```

现在我们可以将它添加到我们的列表之上，这就是教程。

```dart
 @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[700],
      body: SmartRefresher(
        controller: _refreshController,
        enablePullDown: true,
        header: defaultHeader,
        onRefresh: () async {
          await Future.delayed(Duration(seconds: 1));
          _refreshController.refreshCompleted();
        },
        child: CustomScrollView(
          slivers: [
            RefreshBackground(), // <== Add AppBack background
            SliverList(delegate: SliverChildListDelegate(buildList()))
          ],
        ),
      ),
    );
  }
```

如果您运行此代码，您应该会在介绍中看到 gif。唯一的区别是折叠时应用栏的颜色。你可以改变它。