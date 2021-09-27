---
title: "Flutter导航备忘录 命名路由指南"
date: 2021-09-27T16:56:37+08:00
draft: true
---

一个简单的指南，涵盖了在 Flutter 上构建应用程序时遇到的所有导航场景

这不是一个全面的导航指南，它应该更多地作为人们挣扎的常见场景的备忘单，这里没有命名的路线导航。

显示如何使用此备忘单涵盖所有现实世界导航情况的

以下是我们将介绍的要点。

1. 导航到其他页面
2. 以编程方式从页面导航回来
3. 页面关闭后获取结果
4. 覆盖页面上的后退按钮



## 导航到其他页面

Flutter 为我们提供了一个我们可以访问的 Navigator。您为它提供当前的 BuildContext 并要求它执行一些导航。

```dart
Navigator.push(context, new MaterialPageRoute(
  builder: (context) => Page2()
));
```

这会将 page2 推送到您的导航堆栈上。

## 以编程方式从页面导航回来

要从堆栈中删除当前页面，您可以使用导航器上的 pop 调用。

```dart
// Removes the current view
Navigator.pop(context);
```

## 页面关闭后获取结果

在我回答的其中一个问题中，开发人员想知道在关闭新推送的页面时如何在调用页面（导航来自的页面）上重新运行函数。

对我们来说幸运的是 Navigator 调用都是 Futures，所以我们可以等待它们的结果。我们返回结果的方式是将值传递给 .pop 方法。所以你如何处理这个

1. 等待您的导航电话
2. 将值传递给 .pop 函数
3. 当 await 完成检查您的值是否与 2 中传递的值匹配

```dart
 // In your calling widget where you want to navigate from, await your navigation result
 var navigationResult = await Navigator.push(
        context, new MaterialPageRoute(builder: (context) => Page2()));
 
 // Check your navigation result
 if(navigationResult == 'my_value') {
  print('I have received results from the navigation');
 }
 
 // Where you perform your pop call
 Navigator.pop(context, 'my_value');
```

\#Override 页面上的后退按钮如果您不希望后退按钮离开当前视图，您可以使用名为 WillPopScope 的小部件。用它包围您的脚手架小部件，并向 onWillPop 调用返回一个假值。False 告诉系统他们不必处理范围弹出调用。

**注意**：*你不应该用这个（Material App）包围你的整个应用程序。您应该使用您希望功能在其上运行的每页小部件。**为您的页面环绕您的脚手架。***

```dart
@override
Widget build(BuildContext context) {
  return WillPopScope(
    onWillPop: () => Future.value(false),
    child: Scaffold(
      body: Container(
        child: Center(
          child: Text('Page 2',
              style: TextStyle(fontSize: 30.0, fontWeight: FontWeight.bold)),
        ),
      ),
    ),
  );
}
```

如果您仍然希望在应用导航回时返回自定义值，您可以在返回 false 之前执行 pop 调用。

```dart
WillPopScope(
      onWillPop: () async {
          // You can await in the calling widget for my_value and handle when complete.
          Navigator.pop(context, 'my_value');
          return false;
        },
        ...
);
```

通过这几段代码，我能够在构建应用程序时完成我在导航方面所需的一切。
