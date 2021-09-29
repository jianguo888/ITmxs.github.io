---
title: "在Flutter中使用Shimmer包进行加载指示"
date: 2021-09-29T09:01:21+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文我将使用 Shimmer 包来指示您的应用程序中的加载。

任何通过网络发出请求或基本读/写操作的应用程序都会在某个时候很忙。您总是希望向用户提供有关正在发生的事情的反馈，或者在他们等待时减少等待的感觉。在过去的几个月里，我一直在使用循环进度指示器以及一些自定义加载器来显示忙碌状态。今天我想向您展示如何将您的应用程序 UI 渲染为骨架并使用[shimmer 包](https://pub.dev/packages/shimmer)。

请不要介意样式，这只是为了让您了解您可以做什么。在这个应用程序中，我将显示一个项目列表，在加载时我将使用这些项目的假版本，并在上面带有微光效果。如果您对这个术语感到困惑，请查看[shimmer 包的](https://pub.dev/packages/shimmer)页面以获取示例。

## 执行

我们将首先将 shimmer 包添加到我们的 pubspec 文件中。

```yaml
shimmer: ^1.0.0
```

然后我们将创建一个简单的 UI。顶部有一些大文本，带有一个包含列表视图的扩展小部件。列表视图将显示 ListItem 小部件，这是一个非常基本的小部件。唯一的特殊逻辑是当索引等于 -1 时，我们显示一个容器而不是文本列。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData(),
      home: Scaffold(
        body: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: <Widget>[
              SizedBox(
                height: 60,
              ),
              Text(
                'My Awesome List',
                style: TextStyle(fontSize: 40),
              ),
              Expanded(
                  child: ListView.builder(
                itemCount: 10,
                itemBuilder: (context, index) => ListItem(index: index),
              ))
            ]),
      ),
    );
  }
}

class ListItem extends StatelessWidget {
  final int index;
  const ListItem({Key key, this.index});

  @override
  Widget build(BuildContext context) {
    return Container(
      height: 60,
      margin: EdgeInsets.symmetric(vertical: 10.0, horizontal: 5.0),
      decoration: BoxDecoration(borderRadius: BorderRadius.circular(10.0)),
      child: Row(
        children: <Widget>[
          Container(
            width: 50.0,
            height: 50.0,
            margin: EdgeInsets.only(right: 15.0),
            color: Colors.blue,
          ),
          index != -1
              ? Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: <Widget>[
                    Text(
                      'This is title $index',
                      style: TextStyle(fontWeight: FontWeight.bold),
                    ),
                    Text('This is more details'),
                    Text('One more detail'),
                  ],
                )
              : Expanded(
                  child: Container(
                    color: Colors.grey,
                  ),
                )
        ],
      ),
    );
  }
}
```

骨架屏幕由 Facebook 推广（主要是），是减少等待出现的好方法。为了模拟这一点，我们将使用带有延迟的 Future 并在我们等待时显示我们的骨架。创建一个在 3 秒延迟后返回整数列表的未来。

```dart
Future<List<int>> _getResults() async {
    await Future.delayed(Duration(seconds: 3));
    return List<int>.generate(10, (index) => index);
  }
```

然后我们将用 Future 构建器替换当前列表，如果有数据可用，该构建器返回列表。当没有数据时，我们希望显示相同的列表，使用假数据并将项目包裹在微光效果中。替换以下内容

```dart
// old list code
 Expanded(
      child: ListView.builder(
    itemCount: 10,
    itemBuilder: (context, index) => ListItem(index: index),
  ))
```

和

```dart
 Expanded(
    child: FutureBuilder<List<int>>(
        // perform the future delay to simulate request
        future: _getResults(), 
        builder: (context, snapshot) {
          if (!snapshot.hasData) {
            return ListView.builder(
              itemCount: 10,
              // Important code
              itemBuilder: (context, index) => Shimmer.fromColors(
                  baseColor: Colors.grey[400],
                  highlightColor: Colors.white,
                  child: ListItem(index: -1)),
            );
          }

          return ListView.builder(
            itemCount: snapshot.data.length,
            itemBuilder: (context, index) => ListItem(index: index),
          );
        }),
  )
```

我们上面所做的只是告诉代码，当 snapShot 中还没有数据时，`if (!snapshot.hasData)`我们想要显示 10 个项目的列表。列表项是 ListItem 小部件，但我们传递 -1 作为索引而不是传递实际数据。这将用我们的容器替换文本，使其看起来像一个骨架。请参阅下面的屏幕截图。

![Flutter Skeleton View with Shimmer](https://luckly007.oss-cn-beijing.aliyuncs.com/img/014-screenshot1.7d545b4.73ac12466a67fdd951c520fc96b763ae.jpg)