---
title: "使用生成的路由在Flutter中清洁导航"
date: 2021-09-29T08:58:28+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍使用生成的路由在 Flutter 中清洁导航

本教程将介绍如何在 Flutter 中设置路由器，以使导航调用更清晰、更简洁

Flutter 中的导航可以通过以下两种方式之一完成。命名路由，或通过实例化 PageRoute 并将其传递给 Navigator 来显式推送路由。推送路由可以变得非常明确，并且它们之间的共享逻辑变得困难。类似于检查用户是否已针对特定视图进行身份验证的逻辑。在这篇文章中，我们将为 Flutter 设置一个路由器，并允许您使用带参数的命名路由。

## 设置

我们将创建两个视图来使用，您可以将它们全部添加到 main.dart 文件中。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(home: Scaffold(body: Home()));
  }
}

class Home extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text('Home')),
    );
  }
}

class Feed extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text('Feed')),
    );
  }
}
```

### 路由器/路由设置

MaterialApp 为您提供了一个名为的属性`onGenerateRoute`，您可以在其中传入一个返回 a`Route<dynamic>`并接受的函数`RouteSettings`。这就是我们将要使用的。为了保持整洁，我们将创建一个 Router 类。在其中，我们将创建一个具有上述签名的静态函数。创建一个 router.dart 文件。

```dart
class Router {
  Route<dynamic> generateRoute(RouteSettings settings) {
    
  }
}
```

设置包含请求路线的路线信息。它为我们提供了两个关键的东西。名称和参数。我们将使用名称来确定要返回的视图。将 generate 函数更新为如下所示。

```dart
 static Route<dynamic> generateRoute(RouteSettings settings) {
    switch (settings.name) {
      case '/':
        return MaterialPageRoute(builder: (_) => Home());
      case '/feed':
        return MaterialPageRoute(builder: (_) => Feed());
      default:
        return MaterialPageRoute(
            builder: (_) => Scaffold(
                  body: Center(
                      child: Text('No route defined for ${settings.name}')),
                ));
    }
  }
```

这意味着当名称等于“/”时，我们将显示家。当它是“/feed”时，我们将显示提要视图。为了避免在我们的代码中出现任何错误，我们将采用这些硬编码值（魔术值）并将它们放入一个可以在任何地方使用的 constants.dart 文件。

```dart
/// This file contains all the routing constants used within the app

const String homeRoute = '/';
const String feedRoute = 'feed';
```

更新 switch case 语句以使用新变量

```dart
...
    switch (settings.name) {
      case homeRoute:
        return MaterialPageRoute(builder: (_) => Home());
      case feedRoute:
        return MaterialPageRoute(builder: (_) => Feed());
...
```

现在在您的应用程序中，您将在其中定义 MaterialApp，将 generateRoute 函数传递给 onGenerateRoute。要将主视图定义为起始视图，我们将使用 initialRoute 代替将 home 属性设置为小部件。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      onGenerateRoute: Router.generateRoute,
      initialRoute: homeRoute,
    );
  }
}
```

### 导航

现在，当您想要导航时，您只需使用

```dart
Navigator.pushNamed(context, feedRoute);
```

这将导航到 FeedView。如果我们想将参数传递给 Feed 视图，那只是一个小小的改动。让我们的 Feed 视图接受一个字符串作为参数。

```dart
class Feed extends StatelessWidget {

  final String data;

  Feed(this.data);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text('Feed: $data')),
    );
  }
}
```

将浮动操作按钮添加到您的 homeView 和 onPressed，我们将推送提要视图并传入一些字符串数据作为参数。

```dart
class Home extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(onPressed: (){
        Navigator.pushNamed(context, feedRoute, arguments: 'Data from home');
      },),
      body: Center(child: Text('Home')),
    );
  }
}
```

现在在我们的路由器中，我们必须从设置中的参数属性中获取这些数据并将其传递给我们的 Feed。

```dart
 case feedRoute:
    var data = settings.arguments as String;
    return MaterialPageRoute(builder: (_) => Feed(data));
```

如果您点击 HomeView 上的浮动操作按钮，您将导航到 Feed 并看到数据在那里。您可以在参数中传递任何值。只需确保您转换为您想要的类型并将其传递给您的视图。复杂的对象，甚至其他小部件 😉