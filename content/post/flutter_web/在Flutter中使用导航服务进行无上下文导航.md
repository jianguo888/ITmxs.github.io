---
title: "在Flutter中使用导航服务进行无上下文导航"
date: 2021-09-27T16:59:23+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

# 在 Flutter 中使用导航服务进行无上下文导航

本教程介绍了如何设置导航服务以允许您从业务逻辑进行导航，其中上下文不可用。为了实现这个功能，我们将使用导航键来访问我们的`NavigationService`.

*注意：服务功能只能从您的业务逻辑中使用，而不是直接从视图中使用。初始化之外。本教程不会这样做，所以我可以继续讨论主题*

今天我们将使用 get_it 提供我们的导航服务。设置 get_it 已在[此处介绍](https://www.filledstacks.com/snippet/dependency-injection-in-flutter)，大约有 6 行代码，因此请快速完成。

## [执行](https://www.filledstacks.com/snippet/navigate-without-context-in-flutter-with-a-navigation-service/#implementation)

在 Flutter 中 GlobalKeys 可用于访问 a 的状态，`StatefulWidget`这就是我们将用于访问构建上下文之外的 NavigatorState 的内容。我们将创建一个`NavigationService`包含全局键的，我们将在初始化时设置该键，我们将在服务上公开一个函数来导航给定的名称。让我们从`NavigationService`.

```dart
class NavigationService {
  final GlobalKey<NavigatorState> navigatorKey =
      new GlobalKey<NavigatorState>();

  Future<dynamic> navigateTo(String routeName) {
    return navigatorKey.currentState.pushNamed(routeName);
  }

  bool goBack() {
    return navigatorKey.currentState.pop();
  }
}
```

我们将只介绍推送命名路由。您可以在此处设置替换或添加更多附加逻辑的功能，例如检查身份验证，然后显示登录视图而不是预期视图等。

然后我们`NavigationService`用定位器注册。

```dart
void setupLocator() {
  locator.registerLazySingleton(() => NavigationService());
}
```

在主文件中，我们将 GlobalKey 作为 NavigatorKey 传递给我们的`MaterialApp`.

```dart
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: 'Flutter Demo',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        navigatorKey: locator<NavigationService>().navigatorKey,
        home: HomeView());
  }
```

我们将有两个基本视图来显示导航。

```dart
class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text('HomeView'),
      ),
    );
  }
}

class LoginView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text('LoginView'),
      ),
    );
  }
}
```

由于我们使用命名路由，我们必须设置路由。我们将使用 onGenerateRoute。*[有关干净的命名路由方法，请参阅本教程](https://www.filledstacks.com/snippet/clean-navigation-in-flutter-using-generated-routes)*

```dart
Widget build(BuildContext context) {
 return MaterialApp(
    title: 'Flutter Demo',
    theme: ThemeData(
      primarySwatch: Colors.blue,
    ),
    navigatorKey: navigationService.navigatorKey,
    onGenerateRoute: (routeSettings) {
      switch (routeSettings.name) {
        case 'login':
          return MaterialPageRoute(builder: (context) => LoginView());
        default:
          return MaterialPageRoute(builder: (context) => HomeView());
      }
    },
    home: HomeView());
}
```

然后终于，我们可以继续使用导航`NavigationService`在`FloatingActionButton`上HomeView。

```dart
class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          locator<NavigationService>().navigateTo('login');
        },
      ),
      body: Center(
        child: Text('HomeView'),
      ),
    );
  }
}
```

就是这样。现在可以在您的模型中使用此服务进行导航，以便您的导航逻辑可以脱离 UI 并与业务逻辑共享。对这样的[架构的](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide)添加现在也将从您的 UI 文件中提取任何有关导航的逻辑。

