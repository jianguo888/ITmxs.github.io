---
title: "在Flutter中创建功能小部件以减少样板"
date: 2021-09-29T09:11:28+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍在 Flutter 中创建功能小部件以减少样板

在本教程中，我们将通过functional_widget 代码生成器的强大功能将普通的Widget 返回函数转换为完整的无状态小部件。

我们现在知道 StatelessWidgets 比函数更受欢迎，因为它有所有好处。

- 性能优化
- 集成小部件检查器
- 可以定义键
- 获取它自己的上下文

仅举几个。但是，有时我们仍然使用函数，因为“我们只是将一些小部件组合在一起，而且制作起来更快更容易”。我的意思是，写这个

```dart
Widget headerSection(String title, String name) => Column(
  children:[
    Text(title), 
    Text('All posts from $name')
  ]);
```

比这短得多

```dart
class HeaderSection extends StatelessWidget {
  final String title;
  final String name;

  const HeaderSection({Key key, this.title, this.name}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Column(children: [
      Text(title),
      Text('All posts from $name'),
    ]);
  }
}
```

我编写函数来对事物进行分组，即使我知道这样做是错误的。它看起来更好，而且很容易写。写性能较差的代码的糟糕借口，我知道 🙄

## 执行

`functional_widget_annotation`为您提供注释返回小部件的函数的能力，然后告诉功能小部件为您生成无状态小部件代码。我们需要两个包才能工作。在 pubspec 中，在适当的地方添加以下包。

```yaml
dependencies:
  functional_widget_annotation: ^0.5.0

dev_dependencies:
  functional_widget: ^0.6.0
  build_runner: ^1.3.1
```

然后要运行生成器，您将使用 build_runner

```text
flutter pub pub run build_runner watch
```

这将监视源文件夹并在任何更改时生成新的小部件。让我们创建一个带有主视图的示例，一个接收两个字符串的标题小部件和一个显示花哨容器的装饰容器小部件。它的工作方式是根据您修饰的函数为您生成无状态小部件代码。该函数必须返回一个小部件，并且该文件必须是 [filename].g.dart 的一部分。我们将设置`part main.g.dart`. .g.dart 文件包含了所有生成的widget代码，大家可以看一下，这里就不贴了。这是正常的无状态小部件代码。

```dart
// Import packages for functional widget to work
import 'package:flutter/foundation.dart';
import 'package:functional_widget_annotation/functional_widget_annotation.dart';

// include the partial generated file
part 'main.g.dart';

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(home: HomeView('Home view Title'));
  }
}

@widget
Widget homeView(String title) => Scaffold(
        body: Center(
          child: Column(mainAxisSize: MainAxisSize.min, children: [
            HeaderSection(title, 'FilledStacks'),
            const DecoratedContainer(),
        ]),
    ));

@widget
Widget headerSection(String title, String name) => Column(children: [
      Text(title),
      Text('All posts from $name'),
    ]);

@widget
Widget decoratedContainer() => Container(
      width: 100,
      height: 100,
      decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(10.0),
          color: Colors.red,
          boxShadow: [
            BoxShadow(color: Colors.red, blurRadius: 16.0),
          ]),
    );
```

每个函数都会生成一个无状态小部件，使函数名成为以大写字母开头的类名。因此，在引用小部件时，您使用大写名称而不是实际声明的函数名称。例如

```dart
@widget
Widget myCustomWidget() => Container();

// will be used as 

MyCustomWidget();
```

如果您对此没有留下深刻印象或兴奋，那么您可能还没有为单个视图构建 10 个小部件😅这是一个救星。我将继续使用这个。特别是对于仅在一个视图中使用的小部件。