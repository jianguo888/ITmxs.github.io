---
title: "在Flutter中构建主题管理器"
date: 2021-09-29T09:29:59+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

在本教程中，我们将构建一个简单的主题管理器来做这样的事情

![主题交换动画](https://luckly007.oss-cn-beijing.aliyuncs.com/img/theme-swap.82a2fbd.75063343c2e576a0d834a40aa869bae4.gif)

我们将介绍使用`flutter_statusbarcolor`和更改状态栏颜色以及整体主题`provider`。我们将从安装软件包开始。

```yaml
flutter_statusbarcolor: any
provider: ^3.0.0
```

然后我们可以创建一个 HomeView，它将为我们显示我们的主题更改。

```dart
class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Theme.of(context).backgroundColor,
        floatingActionButton: FloatingActionButton(
          onPressed: () {

          },
        ));
  }
}
```

## 主题管理器

正如您从我的其他教程中了解到的，我建议保持对象的职责清晰，并以让您的代码也能显示出这一点为目标。在我们的例子中，改变主题的责任在`ThemeManager`. UI 将只使用主题并对其进行设置，而无需对其更改有任何了解。我们的主题管理器将有一个预定义的列表`availableThemes`以及一个控制器，我们将在其中发出已设置的新主题。我们还将通过公共属性公开控制器的流。

```dart
class ThemeManager {
   StreamController<ThemeData> _themeController = StreamController<ThemeData>();

   List<ThemeData> _availableThemes = [
    ThemeData(backgroundColor: Colors.red, accentColor: Colors.blue),
    ThemeData(backgroundColor: Colors.green, accentColor: Colors.yellow),
    ThemeData(backgroundColor: Colors.purple, accentColor: Colors.pink),
    ThemeData(backgroundColor: Colors.blue, accentColor: Colors.red),
  ];

  Stream<ThemeData> get theme => _themeController.stream;
}
```

### 更新状态栏颜色

主题交换将涉及状态栏颜色的更新，因此我们将其实现为一个单独的函数，我们将在交换主题时调用该函数。

```dart
Future _updateStatusBarColor(ThemeData themeToApply) async {
  // Set status bar color
  await FlutterStatusbarcolor.setStatusBarColor(themeToApply.accentColor);

  // Check the constrast between the colors and set the status bar icons colors to white or dark
  if (useWhiteForeground(themeToApply.accentColor)) {
    FlutterStatusbarcolor.setStatusBarWhiteForeground(true);
  } else {
    FlutterStatusbarcolor.setStatusBarWhiteForeground(false);
  }
}
```

这里我们将 StatusBarColor 设置为主题的重音颜色。此外，我们确保状态栏图标仍然可见，因此我们使用检查对比度`useWhiteForeground`并将图标设置为白色或黑色。

### 更新主题

为了交换主题，我们将简单地跟踪 currentTheme 索引并在函数中增加它。然后我们将使用新索引来获取主题，然后将其添加到控制器上。

```dart
int _currentTheme = 0;

...

Future changeTheme() async {
  _currentTheme++;
  if (_currentTheme >= _availableThemes.length) {
    _currentTheme = 0;
  }

  // Get the theme to apply
  var themeToApply = _availableThemes[_currentTheme];

  // Update status bar color
  await _updateStatusBarColor(themeToApply);
  // Broadcast new theme
  _themeController.add(themeToApply);
}
```

## 提供主题

为了让应用程序使用主题并使其自动更新，我们将使用 Provider。我们将首先将我们的包裹`MaterialApp`在 a 中`MultiProvider`并提供 ThemeManager 作为提供者，然后`Theme`作为 a 提供`StreamProvider`。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
        providers: [
          Provider.value(value: ThemeManager()),
          StreamProvider<ThemeData>(
              builder: (context) =>
                  Provider.of<ThemeManager>(context, listen: false).theme)
        ],
        child: MaterialApp(
          title: 'Theme Manager Demo',
          home: HomeView(),
        ));
  }
}
```

在这里，我们将 注册`ThemeManager`为提供者。然后我们`ThemeManager`在构建器中请求from Provider`StreamProvider`并返回`ThemeData`流。我们还告诉 StreamProvider 不要监听来自 ThemeManager 的更新。如果你使用get_it 设置进行依赖注入，那么它看起来像这样。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
        providers: [
          StreamProvider<ThemeData>(builder: (context) => locator<ThemeManager>().theme)
        ],
        child: MaterialApp(
          title: 'Theme Manager Demo',
          home: HomeView(),
        ));
  }
}
```

接下来，我们必须确保当 ThemeData 更改时，我们会`MaterialApp`使用新主题更新我们的主题。为此，我们将把我们包装`MaterialApp`在一个 Consumer 类型中`ThemeData`，并将主题传递给我们在应用程序上的主题属性。

```dart
Widget build(BuildContext context) {
  return MultiProvider(
    ...
    child: Consumer<ThemeData>(
        builder: (context, theme, child) => MaterialApp(
          title: 'Theme Manager Demo',
          theme: theme,
          home: HomeView(),
        )),
  );
}
```

而我们要做的最后一件事就是调用`changeTheme`我们的管理功能时，`floatingActionButton`在按下`HomeView`。

```dart
class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Theme.of(context).backgroundColor,
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          Provider.of<ThemeManager>(context).changeTheme();
        },
      ),
    );
  }
}
```

那应该这样做。您应该尽可能地将您的职责分开并明确定义。当涉及到 UI 时，脚手架只显示主题提供的颜色。ThemeManager 负责更新到新主题，完成所有计算（在本例中不多），然后广播新主题。其余的应该由架构处理，在这种情况下，提供者将通过调用依赖于 ThemeData 的所有消费者并重建它们来更新主题。