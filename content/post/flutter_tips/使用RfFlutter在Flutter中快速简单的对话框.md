---
title: "使用RfFlutter在Flutter中快速简单的对话框"
date: 2021-09-29T09:12:57+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

在本教程中，我们将使用 RfFlutter 来显示如下对话框。

![教程的所有警报](https://luckly007.oss-cn-beijing.aliyuncs.com/img/018-preview.cbab2cf.8b78cf466fe03e528c27ba06daef7dd1.jpg)

我们从左到右。首先要做的是添加包

```yaml
rflutter_alert: ^1.0.2
```

## 基本警报

RfFlutter 具有看起来不错的基本警报，并且可以轻松使用。我们将设置一个带有 HomeView 无状态小部件的基本应用程序。我将使用功能性小部件，这样我就不会编写太多代码。您可以通过定义整个类来使用普通的无状态小部件。我们将让我们的应用程序带有一个简单的 HomeView 小部件。我们将在整个教程中使用的中心有一个按钮。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(home: HomeView('Home view Title'));
  }
}

@widget
Widget homeView(BuildContext context, String title) => Scaffold(
    body: Center(
        child: MaterialButton(
  color: Colors.yellow,
  child: Text('Show Alert'),
  onPressed: () {
    // Show a basic widget
    Alert(context: context, title: "FilledStacks", desc: "Flutter is awesome.")
        .show();
  },
)));
```

onPressed 函数调用中的代码是我们显示基本小部件所需的全部代码。

## 自定义小部件样式

基本小部件可以设置样式并具有以下属性。

```dart
  final AnimationType animationType;
  final Duration animationDuration;
  final ShapeBorder alertBorder;
  final bool isCloseButton;
  final bool isOverlayTapDismiss;
  final Color overlayColor;
  final TextStyle titleStyle;
  final TextStyle descStyle;
  final EdgeInsets buttonAreaPadding;
```

基于此，您可以发出非常圆润的警报，当您在警报之外点击时不会解除警报。并有一个蓝色覆盖物来指示信息通知。

```dart
 var alertStyle = AlertStyle(
          overlayColor: Colors.blue[400],
          animationType: AnimationType.fromTop,
          isCloseButton: false,
          isOverlayTapDismiss: false,
          descStyle: TextStyle(fontWeight: FontWeight.bold),
          animationDuration: Duration(milliseconds: 400),
          alertBorder: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(50.0),
            side: BorderSide(
              color: Colors.grey,
            ),
          ),
          titleStyle: TextStyle(
            color: Color.fromRGBO(91, 55, 185, 1.0),
          ),
        );

        Alert(
          context: context,
          style: alertStyle,
          type: AlertType.info,
          title: "FilledStacks",
          desc: "FilledStacks.com has the best Flutter tutorials",
          buttons: [
            DialogButton(
              child: Text(
                "COOL",
                style: TextStyle(color: Colors.white, fontSize: 20),
              ),
              onPressed: () => Navigator.pop(context),
              color: Color.fromRGBO(91, 55, 185, 1.0),
              radius: BorderRadius.circular(10.0),
            ),
          ],
        ).show();
```

## 自定义内容

您不仅可以更改样式，还可以添加自己的内容。让我们添加一个警报弹出窗口，当您希望用户再次登录时会显示该弹出窗口。

```dart
   Alert(
        context: context,
        title: "LOGIN",
        content: Column(
          children: <Widget>[
            TextField(
              decoration: InputDecoration(
                icon: Icon(Icons.account_circle),
                labelText: 'Username',
              ),
            ),
            TextField(
              obscureText: true,
              decoration: InputDecoration(
                icon: Icon(Icons.lock),
                labelText: 'Password',
              ),
            ),
          ],
        ),
        buttons: [
          DialogButton(
            onPressed: () => Navigator.pop(context),
            child: Text(
              "LOGIN",
              style: TextStyle(color: Colors.white, fontSize: 20),
            ),
          )
        ]).show();
```

## 某组织

如果您打算在多个地方使用警报并且想要不同的样式，我建议您创建一个对话框帮助文件，您可以在其中存储所有样式和预定义的小部件。

```dart
// dialog_helper.dart


showLoginDialog(
  BuildContext context, {
  TextEditingController usernameController,
  TextEditingController loginController,
  Function onLoginPressed
}) {
  Alert(
      context: context,
      title: "LOGIN",
      content: Column(
        children: <Widget>[
          TextField(
            decoration: InputDecoration(
              icon: Icon(Icons.account_circle),
              labelText: 'Username',
            ),
          ),
          TextField(
            obscureText: true,
            decoration: InputDecoration(
              icon: Icon(Icons.lock),
              labelText: 'Password',
            ),
          ),
        ],
      ),
      buttons: [
        DialogButton(
          onPressed: onLoginPressed,
          child: Text(
            "LOGIN",
            style: TextStyle(color: Colors.white, fontSize: 20),
          ),
        )
      ]).show();
}
```

现在在你的代码中你所要做的就是

```dart
@widget
Widget homeView(BuildContext context, String title) => Scaffold(
        body: Center(
            child: MaterialButton(
      color: Colors.yellow,
      child: Text('Show Alert'),
      onPressed: () {
        showLoginWidget(context,
        onLoginPressed: (){  /* Do stuff */ }); // <-- Much better readability
      },
    )));
```

当您有许多警报样式时，将它们作为 const 存储在帮助文件中并重用它们。就是这样。谢谢阅读。