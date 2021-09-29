---
title: "在Flutter失败时重新运行你的启动逻辑"
date: 2021-09-29T09:21:19+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍在 Flutter 失败时重新运行你的启动逻辑

有时，应用程序必须在启动之前运行异步函数。像加密交易工具这样的东西必须在线，所以他们会在开始时提出登录请求，在线游戏也是如此，或者在我的情况下，一个应用程序在启动时从磁盘（或网络，如果这是第一次）。将它构建到 HomeView 中会很容易，但是一些应用程序，比如我的，根据配置有 4 个不同的启动屏幕，我们不会将它构建到每个视图中。

当启动逻辑失败时，我们希望为用户提供重试的选项。因此，我们需要能够将应用程序再次置于与启动时相同的状态。我们将保持示例简单。当应用程序启动时，我们将运行异步函数。MaterialApp 的主页将是一个 StreamBuilder，它根据流值显示不同的视图。

## 执行

这就是我们将如何实现功能。在启动 Future 以获取重要数据时将运行。在未来，我们将在 StreamController 经历状态时向其添加值。MaterialApp 的 home 将是一个 StreamBuilder，它监听来自前面提到的控制器的流。根据来自该流的值，我们将显示不同的 UI。更具体地说，以下用户界面：

- **NoData / Busy**：带有加载指示器的文本（我们将使用 CircularProgressIndicator，但您也可以使用SpinKit
- **成功**：黄色视图显示主页
- **错误**：带有重试按钮的文本，可重新运行未来以准备好重要数据

我们将从一个基本的应用程序开始

```dart
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Container(),
    );
  }
}
```

我们想在应用程序启动时调用 Future，但我希望将 MyApp 保留为无状态小部件，因此我们将创建一个有状态包装器来为我们执行此操作。创建一个名为 stateful_wrapper.dart 的新文件。它是一个有状态的小部件，它接受一个名为 onInit 的函数和一个子小部件。覆盖 initState 函数并在覆盖中调用 onInit。

```dart
class StatefulWrapper extends StatefulWidget {
  final Function onInit;
  final Widget child;
  StatefulWrapper({Key key, this.onInit, this.child}) : super(key: key);

  _StatefulWrapperState createState() => _StatefulWrapperState();
}

class _StatefulWrapperState extends State<StatefulWrapper> {
  @override
  void initState() {
    widget.onInit();
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return widget.child;
  }
}
```

接下来我们将添加我们将使用的状态枚举。你应该把它放在它自己的文件中，我把它放在 main.dart 文件中作为例子

```dart
enum StartupState { Busy, Success }
```

现在让我们添加将发出我们的状态的 StreamController 和将完成重要工作并将状态添加到流中的未来。

```dart
class MyApp extends StatelessWidget {

  final StreamController<StartupState> _startupStatus = StreamController<StartupState>();

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Container(),
    );
  }

   Future getImportantData({bool isError = false}) async {
    _startupStatus.add(StartupState.Busy);
    await Future.delayed(Duration(seconds: 2));

    if (isError) {
      _startupStatus.add(StartupState.Error);
    } else {
      _startupStatus.add(StartupState.Success);
    }
  }
}
```

现在我们可以将它们联系在一起。我们希望在视图初始化时调用未来，因此我们将使用我们的`StatefulWrapper`并传递一个`onInit`函数。我们将传递 isError true 以便我们可以遍历所有状态。UI 将是一个 Scaffold，根子`StatefulWrapper`节点是我们的，而该包装器的子节点将是一个接受`startupStatus`流的 StreamBuilder 。

```dart
 @override
Widget build(BuildContext context) {
  return MaterialApp(
    home: Scaffold(
      body: StatefulWrapper(
          onInit: () => getImportantData(isError: true),
          child: StreamBuilder<StartupState>(
            stream: _startupStatus.stream,
            builder: (context, snapshot) {
        
            },
          ),
      ),
    ),
  );
}
```

现在我们终于可以添加我们的用户界面了。第一个 UI 位，我们将检查`snapShot`hasData，或者它是否繁忙，我们将显示加载指示器。

```dart
 if (!snapshot.hasData || snapshot.data == StartupState.Busy) {
    return Center(
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: <Widget>[
          Text('Show your app logo here'),
          CircularProgressIndicator()
        ],
      ),
    );
  }
```

然后我们要检查快照是否为错误类型。如果是，我们将返回错误消息以及可用于重试的 IconButton。

```dart
 if (snapshot.hasError) {
    return Center(
        child: Column(
      mainAxisSize: MainAxisSize.min,
      children: <Widget>[
        Text('${snapshot.error} Retry?'),
        IconButton(
          icon: Icon(
            Icons.refresh,
            size: 55,
          ),
          onPressed: () {
            getImportantData();
          },
        )
      ],
    ));
  }
```

在构建器函数的最后，它成功了，我们将显示一个黄色容器。

```dart
return Container(color: Colors.yellow);
```

这就是您可以在应用程序启动时设置简单重试的方法。如果您运行该应用程序，您将看到加载指示，完成后您将收到错误消息。点击重试按钮将重新运行 Future，因此您将再次看到加载，然后您将看到成功。