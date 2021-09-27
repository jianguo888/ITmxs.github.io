---
title: "Flutter导航备忘录"
date: 2021-09-27T16:51:21+08:00
draft: true
---



# Flutter 导航备忘单 - 命名路由指南

一个简单的指南，涵盖了使用命名路由的设置和所有导航场景。

本教程应该作为命名路线导航的备忘单，从设置到等待结果。我们将使用命名路由。

以下是我们将介绍的要点。

1. 设置处理导航的路由器
2. 处理未定义的路由
3. 导航到视图
4. 将参数传递给视图
5. 以编程方式返回
6. 视图关闭后获取结果
7. 覆盖后退按钮 na 视图



## 为命名路由设置路由器

一个`MaterialApp`小工具为我们提供了一个所谓的财产`onGenerateRoute`，我们可以提供`Function`的是发生在一个`RouteSettings`参数，并返回一个`Route<dynamic>`。这是我们将用来执行所有路由的函数。我们将首先清理 main.dart 文件并将我们的`onGenerateRoute`函数设置为路由器中的静态方法。

在 Flutter 文档中提到我们不应该仅仅为了命名空间而使用类。另一种看待它的方式是拥有一个从未实例化的类。这些在 dart 中被视为 codeSmells，因此我们将使用 topLevel 函数。来自 C# 这让我感到不舒服，但它在像 dart 这样的语言中是允许的。我们将使用别名导入，以便在代码中仍然清晰。

```dart
import 'package:named_routing/router.dart' as router;
...
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Named Routing',
      onGenerateRoute: router.generateRoute
    );
  }
}
```

然后我们将在 lib 文件夹中创建一个名为 router.dart 的文件

```dart
import 'package:flutter/material.dart';

Route<dynamic> generateRoute(RouteSettings settings) {
  // Here we'll handle all the routing
}
```

每当我们从 Navigator 请求导航时，都会调用此函数，并且它会期望返回到所请求路径的 Route。接下来，我们将创建一些视图以导航到我们将使用非常有创意的名称`HomeView`，`LoginView`就像我一直做的那样:)

```dart
class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text('Home'),),
    );
  }
}

class LoginView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text('Login'),),
    );
  }
}
```

现在，我们终于可以进入导航了。传递给`generateRoute`函数的参数的类型是`RouteSettings`。此类型包含请求的路由名称以及传递给该参数调用的参数。我们将使用该名称来设置一个 switch 语句，该语句根据名称返回我们的家或我们的登录名。

**注意：**当您将路由映射到“/”并使用“/login”之类的路径时，由于具有深层链接功能，导航器将推送 HomeView，然后推送 LoginView。在进行路由时请记住这一点。

使用 switch 语句更新 generateRoute 函数，该语句`MaterialPageRoute`为每个视图返回 a 。如果您使用的`CupertinoPageRoute`是 iOS 并且想要这些默认转换，您也可以使用 a 。

```dart
Route<dynamic> generateRoute(RouteSettings settings) {
  switch (settings.name) {
    case '/':
      return MaterialPageRoute(builder: (context) => HomeView());
    case 'login':
      return MaterialPageRoute(builder: (context) => LoginView());
    default:
      return MaterialPageRoute(builder: (context) => HomeView());
  }
}
```

这里的代码非常简单。如果名称与定义的大小写匹配，则我们返回一个路由，该路由为视图返回该小部件。现在我们将在`HomeView`没有定义的路由/匹配名称时返回。我将在本文后面介绍两种处理未定义路径的方法。

最后要做的是确保我们永远不会打字错误，所以我们将我们的路由名称存储在一个名为 routing_constants.dart 的单独文件中

```dart
const String HomeViewRoute = '/';
const String LoginViewRoute = 'login';
```

然后我们可以用这些参数替换硬编码的案例。

```dart
  switch (settings.name) {
    case HomeViewRoute:
      ...
    case LoginViewRoute:
      ...
  }
```

最后要做的是告诉应用程序从哪个视图开始，然后设置完成。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Named Routing',
      onGenerateRoute: router.generateRoute,
      initialRoute: HomeViewRoute,
    );
  }
}
```

## 处理未定义的路由

有两种方法可以处理未定义的路由。

1. 将您的 UndefinedView 作为默认路由返回 `generateRoute`
2. 从 `onUnknownRoute`

我们将从创建我们的`UndefinedView`. 创建一个名为 undefined_view.dart 的新文件

```dart
class UndefinedView extends StatelessWidget {
  final String name;
  const UndefinedView({Key key, this.name}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text('Route for $name is not defined'),
      ),
    );
  }
}
```

### 1. 从 generateRoute 返回未定义的路由

我更喜欢使用这种方法。尽管 Flutter 为您提供了一种为未定义路径定义路由的方法，但我还是喜欢将所有路由代码放在一起。这种方式很容易做到。让我们返回`UndefinedView`作为默认值并传递未知路由的名称以显示。

```dart
switch (settings.name) {
  ...
  default:
    return MaterialPageRoute(builder: (context) => UndefinedView(name: settings.name,));
}
```

### 2. 从 onUnknownRoute 返回未定义的路由

使用此方法，我们将使用相同的代码，但我们会将其设置在`MaterialApp`.

```dart
return MaterialApp(
  title: 'Named Routing',
  onGenerateRoute: router.generateRoute,
  onUnknownRoute: (settings) => MaterialPageRoute(
      builder: (context) => UndefinedView(
            name: settings.name,
          )),
  initialRoute: HomeViewRoute,
);
```

## 导航到LoginView

现在让我们使用导航。在`HomeView`create a 上`FloatingActionButton`，我们将在`onPressed`函数中导航。我们将导航到`LoginView`.

```dart
// Perform navigation to LoginView
Navigator.pushNamed(context, LoginViewRoute);
```

现在点击该按钮应该会带您到带有上述代码的登录视图。

## 将参数传递给LoginView

要将参数传递给视图，请使用导航调用上的参数属性。这接受任何对象，因此您可以传递自定义类或基本原始类型。我们将传入一个字符串并将其显示在登录视图中。更新您的`pushNamed`调用并为其提供参数值。

```dart
Navigator.pushNamed(context, LoginViewRoute, arguments: 'Data Passed in');
```

在`LoginView`不以任何值，取又那么让更新，第一。

```dart
class LoginView extends StatelessWidget {
  final String argument;
  const LoginView({Key key, this.argument}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return WillPopScope(
      onWillPop: () async {
        Navigator.pop(context, 'fromLogin');
        return false;
      },
      child: Scaffold(
        floatingActionButton: FloatingActionButton(
          onPressed: () {
            Navigator.pop(context, 'fromLogin');
          },
        ),
        body: Center(
          child: Text('Login $argument'),
        ),
      ),
    );
  }
}
```

然后我们就可以提取这些参数，并将其传递到`LoginView`的`router`。

```dart
switch (settings.name) {
  ...
 case LoginViewRoute:
      var loginArgument = settings.arguments;
      return MaterialPageRoute(builder: (context) => LoginView(argument: loginArgument));
}
```

这就是传递参数并提取它们的全部内容。

## 以编程方式返回

这在所有形式的导航中都保持不变。当您想返回时，请使用`pop`导航器上的调用。在登录视图中添加一个浮动操作按钮并在调用中调用以下代码`onPressed`。

```dart
Navigator.pop(context);
```

这将带您返回主视图。

## 页面关闭后获取结果

导航调用 Flutter 是`Futures<dynamic>`，这意味着我们可以期待在操作完成后返回到我们的调用代码。这对我们来说意味着我们可以等待导航调用并期望返回一个结果。将 onPressed 中的更改`HomeView`为此。

```dart
// Navigate to LoginView and wait for a result to come back
var result = await Navigator.pushNamed(context, LoginViewRoute);

// If the result matches show a dialog
if (result == 'fromLogin') {
  showDialog(
    context: context,
    builder: (context) => AlertDialog(
          title: Text('From Login'),
        ));
}
```

在这里，我们正在等待与“fromLogin”匹配的结果，如果匹配，我们将显示一个对话框。如果您正常按回，则不会显示对话框。你必须从你的路由中返回一个值，你这样做的方式是通过`pop`调用。`pop`像这样向您的呼叫添加一个附加参数。

```dart
Navigator.pop(context, 'fromLogin');
```

现在，当您返回时，您会看到警报对话框出现。附加值是动态的，因此您可以传递任何您想要的内容。

## 覆盖页面上的后退按钮

如果您不希望后退按钮离开当前视图，您可以使用名为 WillPopScope 的小部件。用它包围您的脚手架小部件，并向 onWillPop 调用返回一个假值。False 告诉系统他们不必处理范围弹出调用。

**注意**：*你不应该用这个包围你的整个应用程序。您应该使用您希望功能在其上运行的每页小部件。**为您的页面环绕您的脚手架。***

```dart
@override
Widget build(BuildContext context) {
  return WillPopScope(
    onWillPop: () => Future.value(false),
    child: Scaffold(
      ...
    ),
  );
}
```

如果您仍然希望在应用导航回时返回自定义值，您可以在返回 false 之前执行 pop 调用。

```dart
WillPopScope(
  onWillPop: () async {
      Navigator.pop(context, 'fromLogin');
      return false;
    },
    ...
);
```

现在，当您返回时，这会将您的 fromLogin 结果返回到您的调用页面。这就是 Flutter 中导航的全部内容。
