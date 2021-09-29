---
title: "使用Flutter——SignIn实现轻松登录按钮样式"
date: 2021-09-29T09:32:23+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍使用 Flutter SignIn 实现轻松登录按钮样式

您可以使用[flutter_signin_button](https://pub.dev/packages/flutter_signin_button)包来加快开发速度，而不是在开发时设计自己的登录/注册按钮。这篇文章将向您展示可用的按钮以及样式选项。

### 安装

为了让它工作，你必须安装 signin_button 包和 font Awesome。将此添加到您的 pubspec

```yaml
flutter_signin_button: ^0.2.8
font_awesome_flutter: ^8.4.0
```

### 用法

为了展示如何使用它，我们将在 MaterialApp 中显示一个脚手架作为主体。

```dart
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: 'Flutter Demo',
        home: Scaffold(
          body: Container(
            width: double.infinity,
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.center,
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
              ],
            ),
          ),
        ));
  }
}
```

我们将使用按钮并调整它们的样式。以下是所有可以使用的按钮类型。

```dart
enum Buttons {
  Email,
  Google,
  Facebook,
  GitHub,
  LinkedIn,
  Pinterest,
  Tumblr,
  Twitter
}
```

要使用按钮，您要做的就是构造小部件并为其指定类型和 onPressed。

```dart
 children: <Widget>[
  SignInButton(
    Buttons.Google,
    onPressed: () {},
  ),
],
```

您还可以将按钮设置为迷你（请参阅标题图片中的 facebook）

```dart
 SignInButton(
    Buttons.Facebook,
    onPressed: () {},
    mini: true,
  ),
                
```

不在迷你模式下时，您还可以更新要显示的文本。（见标题中的推特）

```dart
SignInButton(
    Buttons.Twitter,
    onPressed: () {},
    text: "Follow FilledStacks on Twitter",
  ),
```