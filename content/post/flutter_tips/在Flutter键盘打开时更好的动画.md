---
title: "在Flutter键盘打开时更好的动画"
date: 2021-09-29T09:27:59+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

在本教程中，我们将仔细聆听键盘可见性，并在它出现时为您的视图提供更平滑的过渡。我们将使用 keyboard_visibility 包来监听可见性，并使用 AnimatedContainer 来为我们的视图 UI 设置动画。

## 听键盘

我们将首先将包添加到 pubspec

```yaml
keyboard_visibility: ^0.5.6
```

我们将有一个简单的设置。我们的 MyApp 小部件将显示一个 HomeView 作为 MaterialApp 的主页。HomeView 将是一个有状态的小部件。

```dart
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: HomeView(),
    );
  }
}

class HomeView extends StatefulWidget {
  const HomeView({Key key}) : super(key: key);

  @override
  _HomeViewState createState() => _HomeViewState();
}

class _HomeViewState extends State<HomeView> {
  @override
  Widget build(BuildContext context) {
    return Scaffold();
  }
}
```

在 initState 函数中，我们将监听键盘可见性。

```dart
 @override
  void initState() {
    KeyboardVisibilityNotification().addNewListener(
      onChange: (bool visible) {
        print('keyboard $visible');
      },
    );

    super.initState();
  }
```

## 动画 UI

假设我们有一个 UI，其中文本字段位于屏幕中央。当我们点击它时，我们希望它到达顶部。为此，我们将使用一个动画容器作为我们的根小部件，并在键盘显示时将对齐设置为顶部中心。让我们首先制作我们的 UI。使您的构建小部件看起来像这样。

```dart
 @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: AnimatedContainer(
        curve: Curves.easeOut,
        duration: Duration(
          milliseconds: 400,
        ),
        width: double.infinity,
        height: double.infinity,
        padding: const EdgeInsets.all(20),
        alignment: Alignment.center,
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: <Widget>[
            Text(
              'Let\'s start your search',
              style: TextStyle(fontSize: 30),
            ),
            TextField(
              decoration: InputDecoration(
                  hasFloatingPlaceholder: true, hintText: 'Enter things here'),
            )
          ],
        ),
      ),
    );
```

这应该会在屏幕中央为您提供一个标题和文本字段。接下来让我们制作动画，我们首先将对齐值作为类变量并在 setState 中更改它的值。

```dart
// Add variable to top of class
Alignment childAlignment = Alignment.center;

@override
void initState() {
  KeyboardVisibilityNotification().addNewListener(
    onChange: (bool visible) {
      // Add state updating code
      setState(() {
        childAlignment = visible ? Alignment.topCenter : Alignment.center;
      });
    },
  );

  super.initState();
}

// Updated animated container alignment property
...
AnimatedContainer(
  curve: Curves.easeOut,
  duration: Duration(
    milliseconds: 400,
  ),
  width: double.infinity,
  height: double.infinity,
  padding: const EdgeInsets.all(20),
  alignment: childAlignment, // <=== Make sure to use childAlignment here
  child: Column(...),
);
```

当您现在点击输入字段时，您会看到列以比通常的键盘跳转更流畅的方式显示在屏幕顶部。