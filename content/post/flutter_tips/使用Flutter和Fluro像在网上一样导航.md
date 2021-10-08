---
title: "使用Flutter和Fluro像在网上一样导航"
date: 2021-09-29T09:15:18+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍使用 Flutter 和 Fluro 像在web上一样导航

[Fluro](https://pub.dev/packages/fluro)是一个路由库，它为我们处理了一些基础知识，并为我们提供了一些类似 web 的路由功能。在本教程中，我将向您展示如何设置 Fluro、使用内置转换以及使用类似 Web 的路由。

## 设置

我们首先将 Fluro 添加到我们的项目中。

```yaml
fluro: 1.4.0
```

然后以与我们命名的路由教程相同的方式，我们将创建一个新文件，这次称为 fluro_router.dart。

```dart
import 'package:fluro/fluro.dart';

class FluroRouter {
  static Router router = Router();
}
```

Fluro 为您提供了一个路由器，您可以在其中定义路径名和它接收的变量，类似于某些 Web 框架中的路由。我们将创建一个可以在应用程序开始运行之前从 main 调用的静态方法。

```dart
// In fluro_router.dart class
static void setupRouter() {
}


// in main.dart
void main() {
  FluroRouter.setupRouter();
  runApp(MyApp());
}
```

现在我们可以定义我们的处理程序。要定义路由，您必须提供一个处理程序。处理程序有一个可选类型和一个 handlerFunc。handlerFunc 接受一个上下文和一个 Map<String, List> 参数并返回一个小部件。返回的小部件是路由器将显示的。我们将创建两个视图来显示导航。

```dart
class LoginView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.yellow[400],
      floatingActionButton: FloatingActionButton(
        onPressed: () {

        },
      ),
      body: Center(
          child: Text(
        this.runtimeType.toString(),
        style: TextStyle(fontSize: 23.0, fontWeight: FontWeight.bold),
      )),
    );
  }
}

class HomeView extends StatelessWidget {
  final String data;
  HomeView(this.data);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.blue[400],
      body: Center(
          child: Text(
            data,
            style: TextStyle(fontSize: 23.0, fontWeight: FontWeight.bold),
      )),
    );
  }
}
```

主页视图接受一个字符串，登录有一个浮动操作按钮，我们将在其中添加导航调用。在我们可以导航之前，我们必须使用 Fluro 定义我们的路线并为其提供处理程序。先登录。

```dart
static Handler _loginHandler = Handler(
    handlerFunc: (BuildContext context, Map<String, dynamic> params) =>
        LoginView());
```

login 是一个静态 Handler 变量，带有一个返回 LoginView 的 handlerFunc。非常简单，我们仍然需要向路由器注册处理程序。为此，我们使用定义。

```dart
static void setupRouter() {
    router.define("login", handler: _loginHandler);
}
```

这里我们告诉路由器，当namedRoute登录被推送时，调用我上面定义的_loginHandler。还剩下一件事要做，我们将完成所有设置以显示一个视图。转到 main.dart 文件并为 onGenerateRoute 提供 Fluro 路由器的生成器。我们还将设置初始路由以登录

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        initialRoute: 'login',
        onGenerateRoute: FluroRouter.router.generator);
  }
}
```

如果您现在运行它，您的应用程序将在登录时启动并且您正在使用 Fluro 路由器 😁

## 像在web上一样导航

Fluro 导航与 Flutter Navigator 相关联，因此导航仍然相同。您 pushNamedRoute 它将通过 Fluro 定义的路由来确定您期望该路由的小部件。Fluro 的一件很酷的事情是您可以导航和传递查询参数。作为示例，我们将为 HomeView 定义一个路由。

```dart
// Define the home view handler
static Handler _homeViewHandler = Handler(
    handlerFunc: (BuildContext context, Map<String, dynamic> params) =>
        HomeView(params['userId'][0]));

static void setupRouter() {
  ...
  // define the route
  router.define("home/:userId", handler: _homeViewHandler);
}
```

处理程序几乎相同，除了使用 userId 在 params 值中建立索引并在其中传递第一个项目。此用户 ID 是在 setupRouter 函数中使用路径“home/:userId”定义的。':' 告诉 Fluro 您将向路径传递一个参数，并且他们应该将其提取到 params 映射中供您使用。最后，我们必须对主视图进行实际导航。转到登录视图并在 onPressed 函数中添加导航调用。

```dart
...
floatingActionButton: FloatingActionButton(
    onPressed: () {
      Navigator.pushNamed(context, 'home/90');
    },
  ),
...
```

我们将使用 Navigator 并推送一个命名路由，传入 90 作为用户 ID。如果您运行它并按下按钮，您将导航到 Home，屏幕上显示值 90。

## 过渡

Fluro 有一些内置的转换，准确地说如下。

```dart
enum TransitionType {
  native,
  nativeModal,
  inFromLeft,
  inFromRight,
  inFromBottom,
  fadeIn,
  custom, // if using custom then you must also provide a transition
}
```

您可以为每个路由定义一个过渡，我们将只使用淡入淡出来保持简单并展示功能性。你应该玩弄这些值并检查它们。在我们定义路由的路由器文件中，在末尾添加一个新属性并传入您想要的过渡。

```dart
static void setupRouter() {
  router.define("login",
      handler: _loginHandler, transitionType: TransitionType.fadeIn);
  router.define("home/:userId",
      handler: _homeViewHandler, transitionType: TransitionType.fadeIn);
}
```

## 多个参数

如果要传递多个参数值，则不必在路径中定义它们中的任何一个。您只需使用 Navigator 导航并像传递普通 url 查询参数一样传递它。这将使其在处理程序的参数函数中可用。例如，您可以深度链接登录路径。

```dart
// Example push
Navigator.pushNamed('login?name=FilledStacks&account=2');


// Handler
static Handler _loginHandler =
    Handler(handlerFunc: (BuildContext context, Map<String, dynamic> params) {
  var name = params['name']?.first;
  var account = params['account']?.first;

  // Use name and account values
  return LoginView();
});
```

如果您不传递复杂的对象，这是一种非常方便的设置路由的方法。