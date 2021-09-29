---
title: "在Flutter中MultiFab菜单与unicorndial"
date: 2021-09-29T09:35:11+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

在本教程中，我们将使用 unicorndial 创建一个如下所示的配置文件菜单。它将显示您的用户图标并动画显示一些注销、设置和主页选项。

![多晶圆厂示例](https://luckly007.oss-cn-beijing.aliyuncs.com/img/fab-example.82a2fbd.321be3d099fa3a38c5e2cc6eca7d4a4a.gif)

首先，我们将包添加到 pubspec

```yaml
unicorndial: ^1.1.5
```

## 用法

在`UnicornDialer`将取代floatingActionButton的使用，我们将与多个FAB的，因为它的孩子们提供它。我们将从创建我们想要的孩子开始。我们将创建一个返回 a 的函数，该函数`List<UnicornButton>`将为`UnicornButton`我们填充一个列表类型。

```dart
 List<UnicornButton> _getProfileMenu() {
    List<UnicornButton> children = [];

    // Add Children here

    return children;
 }
```

我们也知道我们希望所有按钮都相同，因此我们可以创建一个函数`_profileOption`，该函数返回一个带有我们选项样式的 UnicornButton。这个函数将接收我们想要显示的图标以及一个 onPressed 函数。

```dart
Widget _profileOption({IconData iconData, Function onPressed}) {
  return UnicornButton(
      currentButton: FloatingActionButton(
    backgroundColor: Colors.grey[500],
    mini: true,
    child: Icon(iconData),
    onPressed: onPressed,
  ));
}
```

让我们在`_getProfileMenu`函数中为我们的孩子添加三个选项。

```dart
List<UnicornButton> _getProfileMenu() {
  List<UnicornButton> children = [];

  // Add Children here
  children.add(_profileOption(iconData: Icons.account_balance, onPressed:() {}));
  children.add(_profileOption(iconData: Icons.settings, onPressed: (){}));
  children.add(_profileOption(iconData: Icons.subdirectory_arrow_left, onPressed: () {}));

  return children;
}
```

然后我们可以添加`UnicornDialer`FloatingActionButton 并将其提供给我们的孩子。

```dart
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: UnicornDialer(
        parentButtonBackground: Colors.grey[700],
        orientation: UnicornOrientation.HORIZONTAL,
        parentButton: Icon(Icons.person),
        childButtons: _getProfileMenu(),
      ),
      body: Center(child: Text('User Profile'),),
    );
  }
```

我希望你觉得这很有用。