---
title: "Flutter中的自定义启动逻辑"
date: 2021-09-29T08:55:32+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍Flutter 中的自定义启动逻辑

当用户登录或完成注册时，在启动时显示不同的视图。

大多数应用程序需要自定义启动功能。我的意思是我们希望在应用程序启动时根据某个值显示不同的视图。如果用户已注册，您不想再次显示注册屏幕，而是登录屏幕。如果用户已登录，则是主屏幕。

今天我们将介绍这三个屏幕。我们将使用[shared_preferences](https://pub.dev/packages/shared_preferences)包来跟踪会话之间的值。我们将构建一个小应用程序，根据我们在应用程序中的使用程度显示不同的屏幕。

## 设置

将共享首选项添加到您的 pubspec 并[像这样](https://www.filledstacks.com/snippet/shared-preferences-service-in-flutter-for-code-maintainability)设置您的服务。我们将在本教程中添加所有属性。此外，您还可以[设置依赖注入](https://www.filledstacks.com/snippet/dependency-injection-in-flutter)，这不是必需的，但对于本教程来说很不错。这只是几行代码。

您必须从依赖注入设置更改的一件事是在您的主要方法中。由于服务单例是使用 Future 创建的，因此您必须确保在运行应用程序之前等待它完成。

```dart
// There are much better ways to handle this but it's outside of the scope of this tutorial
Future<void> main() async {
  try {
    await setupLocator();
    runApp(MyApp());
  } catch(error) {
    print('Locator setup has failed');
  }
}
```

## 执行

我们需要做的第一件事是将我们的属性添加到服务中。

```dart
  static const String SignedUpKey = 'signedUp';
  static const String LoggedInKey = 'loggedIn';

  ...

  bool get hasSignedUp => _getFromDisk(SignedUpKey) ?? false;
  set hasSignedUp(bool value) => _saveToDisk(SignedUpKey, value);

   bool get hasLoggedIn => _getFromDisk(LoggedInKey) ?? false;
  set hasLoggedIn(bool value) => _saveToDisk(LoggedInKey, value);
```

*如果您对此代码感到困惑，请[像这样](https://www.filledstacks.com/snippet/shared-preferences-service-in-flutter-for-code-maintainability)设置您的 shared_preferences 服务。或者使用直接访问 ie`preferences.setBool(...)`和`preferences.getBool(...)`。不那么优雅，但工作会完成。*

创建三个视图文件 login.dart、signup.dart 和 home.dart，所有这些文件都使用基本的有状态小部件和一个脚手架。复制下面的代码并将其放入您的文件中，适当地重命名类并调整背景颜色。登录黄色[400]，注册红色[400]和家蓝色[400]。

```dart
import 'package:flutter/material.dart';

class LoginView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.blue[400],
      floatingActionButton: FloatingActionButton(onPressed: (){}),
      body: Center(
          child: Text(
            this.runtimeType.toString(),
            style: TextStyle(fontSize: 23.0, fontWeight: FontWeight.bold),
      )),
    );
  }
}
```

现在是主要逻辑的时候了。我们将在 main.dart 文件中创建一个调用的函数，`getStartupScreen()`在那里我们将使用我们的`LocalStorageService`来执行我们的检查并返回适当的视图。

```dart
class MyApp extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: _getStartupScreen()
    );
  }

  Widget _getStartupScreen() {
    var localStorageService = locator<LocalStorageService>();

    if(!localStorageService.hasSignedUp) {
      return SignUpView();
    }

    if(!localStorageService.hasLoggedIn) {
      return LoginView();
    }

    return HomeView();
  }
}
```

这就是 UI 方面。现在我们需要做的就是设置按下浮动操作按钮时的值。在`onPressed`上`SignUpView`获得storageService和signedUp值设置为true，并导航到主视图。

```dart
...
floatingActionButton: FloatingActionButton(onPressed: (){
  locator<LocalStorageService>().hasSignedUp = true;
  Navigator.push(context, MaterialPageRoute(builder: (context) => HomeView()));

  // If you're using preferences directly set it here
},),
...
```

运行应用程序，您应该会看到 SignUpView。

![注册视图开始屏幕](https://luckly007.oss-cn-beijing.aliyuncs.com/img/010-login-view.7d545b4.afc83d3f2004ddf14d696b25431962f2.jpg)

单击浮动操作按钮，您将看到蓝色的 HomeView。现在关闭应用程序并重新启动它。当它启动时，您将看到黄色登录视图。

![注册重启后的登录视图](https://luckly007.oss-cn-beijing.aliyuncs.com/img/010-login-view.7d545b4.afc83d3f2004ddf14d696b25431962f2.jpg)

将上面相同的代码添加到 LoginView floatingActionButton 但更改`hasSignedUp`为`hasLoggedIn`. 按下浮动操作按钮，您应该会再次看到 HomeView。现在，当您关闭应用程序并重新启动时，您将直接进入主视图。

![loginc 重启后的主页视图](https://luckly007.oss-cn-beijing.aliyuncs.com/img/010-login-view.7d545b4.afc83d3f2004ddf14d696b25431962f2.jpg)

你有它。您可以在 main.dart 文件中添加多个逻辑片段，但通过这种方法，您可以轻松管理所有启动逻辑。