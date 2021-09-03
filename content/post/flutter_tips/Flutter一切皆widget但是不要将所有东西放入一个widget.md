---
title: "Flutter一切皆widget但是不要将所有东西放入一个widget"
date: 2021-08-21T09:45:13+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍Flutter一切皆widget但是不要将所有东西放入一个widget

<!--more-->

作为 Flutter 开发人员，我相信您在您的开发生活中至少听说过这句流行的句子：“**一切都是widget”。这是 Flutter 的口头禅，它揭示了这个非常好的 SDK 的内在力量！

当我们在[widgets目录](https://flutter.dev/docs/development/ui/widgets)中，我们可以看到很多小部件，如`Padding`，`Align`，`SizedBox`，等。我们通过组合它们来创建其他小部件，我发现这种方法可扩展、强大且易于理解。

但是当我阅读 一些我在互联网上找到的或由新采用者编写的源代码时，有一件让我震惊的事情：**拥有大量**build**`**方法的趋势，实例化很多小部件！我发现这很难阅读、理解和维护。

作为软件开发人员，我们必须记住，软件的真实生活从第一次发布给用户开始。该软件的源代码将由其他人（包括您未来的您）阅读和维护，这就是为什么保持我们的代码简单、易于阅读和理解非常重要。

“小部件中的一切”的示例可以在[Flutter 文档本身中找到](https://flutter.dev/docs/development/ui/layout/tutorial)。本教程的目标是展示如何构建此布局：

![image-20210822082626144](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210822082626144.png)

最终代码达到了它的目的：展示如何简单地创建上述布局。正如我们所见，甚至还有一些变量和方法可以为布局的各个部分提供语义。这是一个很好的观点，因为它使代码更容易理解。

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Widget titleSection = Container(
      padding: const EdgeInsets.all(32),
      child: Row(
        children: [
          Expanded(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Container(
                  padding: const EdgeInsets.only(bottom: 8),
                  child: Text(
                    'Oeschinen Lake Campground',
                    style: TextStyle(
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                ),
                Text(
                  'Kandersteg, Switzerland',
                  style: TextStyle(
                    color: Colors.grey[500],
                  ),
                ),
              ],
            ),
          ),
          Icon(
            Icons.star,
            color: Colors.red[500],
          ),
          Text('41'),
        ],
      ),
    );

    Color color = Theme.of(context).primaryColor;

    Widget buttonSection = Container(
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: [
          _buildButtonColumn(color, Icons.call, 'CALL'),
          _buildButtonColumn(color, Icons.near_me, 'ROUTE'),
          _buildButtonColumn(color, Icons.share, 'SHARE'),
        ],
      ),
    );

    Widget textSection = Container(
      padding: const EdgeInsets.all(32),
      child: Text(
        'Lake Oeschinen lies at the foot of the Blüemlisalp in the Bernese '
        'Alps. Situated 1,578 meters above sea level, it is one of the '
        'larger Alpine Lakes. A gondola ride from Kandersteg, followed by a '
        'half-hour walk through pastures and pine forest, leads you to the '
        'lake, which warms to 20 degrees Celsius in the summer. Activities '
        'enjoyed here include rowing, and riding the summer toboggan run.',
        softWrap: true,
      ),
    );

    return MaterialApp(
      title: 'Flutter layout demo',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Flutter layout demo'),
        ),
        body: ListView(
          children: [
            Image.asset(
              'images/lake.jpg',
              width: 600,
              height: 240,
              fit: BoxFit.cover,
            ),
            titleSection,
            buttonSection,
            textSection,
          ],
        ),
      ),
    );
  }

  Column _buildButtonColumn(Color color, IconData icon, String label) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Icon(icon, color: color),
        Container(
          margin: const EdgeInsets.only(top: 8),
          child: Text(
            label,
            style: TextStyle(
              fontSize: 12,
              fontWeight: FontWeight.w400,
              color: color,
            ),
          ),
        ),
      ],
    );
  }
}
```

事实上，情况可能更糟。这是我不喜欢的这段代码的典型多合一小部件版本：。

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Color color = Theme.of(context).primaryColor;
    return MaterialApp(
      title: 'Flutter layout demo',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Flutter layout demo'),
        ),
        body: ListView(
          children: [
            Image.asset(
              'images/lake.jpg',
              width: 600,
              height: 240,
              fit: BoxFit.cover,
            ),
            Container(
              padding: const EdgeInsets.all(32),
              child: Row(
                children: [
                  Expanded(
                    child: Column(
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        Container(
                          padding: const EdgeInsets.only(bottom: 8),
                          child: Text(
                            'Oeschinen Lake Campground',
                            style: TextStyle(
                              fontWeight: FontWeight.bold,
                            ),
                          ),
                        ),
                        Text(
                          'Kandersteg, Switzerland',
                          style: TextStyle(
                            color: Colors.grey[500],
                          ),
                        ),
                      ],
                    ),
                  ),
                  Icon(
                    Icons.star,
                    color: Colors.red[500],
                  ),
                  Text('41'),
                ],
              ),
            ),
            Container(
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                children: [
                  Column(
                    mainAxisSize: MainAxisSize.min,
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: [
                      Icon(Icons.call, color: color),
                      Container(
                        margin: const EdgeInsets.only(top: 8),
                        child: Text(
                          'CALL',
                          style: TextStyle(
                            fontSize: 12,
                            fontWeight: FontWeight.w400,
                            color: color,
                          ),
                        ),
                      ),
                    ],
                  ),
                  Column(
                    mainAxisSize: MainAxisSize.min,
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: [
                      Icon(Icons.near_me, color: color),
                      Container(
                        margin: const EdgeInsets.only(top: 8),
                        child: Text(
                          'ROUTE',
                          style: TextStyle(
                            fontSize: 12,
                            fontWeight: FontWeight.w400,
                            color: color,
                          ),
                        ),
                      ),
                    ],
                  ),
                  Column(
                    mainAxisSize: MainAxisSize.min,
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: [
                      Icon(Icons.share, color: color),
                      Container(
                        margin: const EdgeInsets.only(top: 8),
                        child: Text(
                          'SHARE',
                          style: TextStyle(
                            fontSize: 12,
                            fontWeight: FontWeight.w400,
                            color: color,
                          ),
                        ),
                      ),
                    ],
                  ),
                ],
              ),
            ),
            Container(
              padding: const EdgeInsets.all(32),
              child: Text(
                'Lake Oeschinen lies at the foot of the Blüemlisalp in the Bernese '
                'Alps. Situated 1,578 meters above sea level, it is one of the '
                'larger Alpine Lakes. A gondola ride from Kandersteg, followed by a '
                'half-hour walk through pastures and pine forest, leads you to the '
                'lake, which warms to 20 degrees Celsius in the summer. Activities '
                'enjoyed here include rowing, and riding the summer toboggan run.',
                softWrap: true,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

在第二个版本中，我们有一个大`build`方法的小部件，它很难阅读、理解和维护。

现在让我们看看我将如何重写它：

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter layout demo',
      home: const HomePage(),
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Flutter layout demo'),
      ),
      body: ListView(
        children: [
          const _Header(),
          const _SubHeader(),
          const _Buttons(),
          const _Description(),
        ],
      ),
    );
  }
}

class _Header extends StatelessWidget {
  const _Header({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Image.asset(
      'images/lake.jpg',
      width: 600,
      height: 240,
      fit: BoxFit.cover,
    );
  }
}

class _SubHeader extends StatelessWidget {
  const _SubHeader({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: const EdgeInsets.all(32),
      child: Row(
        children: [
          Expanded(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                const _Title(),
                const _SubTitle(),
              ],
            ),
          ),
          const _Likes(),
        ],
      ),
    );
  }
}

class _Title extends StatelessWidget {
  const _Title({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: const EdgeInsets.only(bottom: 8),
      child: Text(
        'Oeschinen Lake Campground',
        style: TextStyle(
          fontWeight: FontWeight.bold,
        ),
      ),
    );
  }
}

class _SubTitle extends StatelessWidget {
  const _SubTitle({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Text(
      'Kandersteg, Switzerland',
      style: TextStyle(
        color: Colors.grey[500],
      ),
    );
  }
}

class _Likes extends StatelessWidget {
  const _Likes({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Row(
      children: <Widget>[
        Icon(
          Icons.star,
          color: Colors.red[500],
        ),
        Text('41'),
      ],
    );
  }
}

class _Buttons extends StatelessWidget {
  const _Buttons({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: [
          const _Button(icon: Icons.call, text: 'CALL'),
          const _Button(icon: Icons.share, text: 'ROUTE'),
          const _Button(icon: Icons.share, text: 'SHARE'),
        ],
      ),
    );
  }
}

class _Button extends StatelessWidget {
  const _Button({
    Key key,
    @required this.icon,
    @required this.text,
  })  : assert(icon != null),
        assert(text != null),
        super(key: key);

  final IconData icon;
  final String text;

  @override
  Widget build(BuildContext context) {
    Color color = Theme.of(context).primaryColor;

    return Column(
      mainAxisSize: MainAxisSize.min,
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Icon(icon, color: color),
        Container(
          margin: const EdgeInsets.only(top: 8),
          child: Text(
            text,
            style: TextStyle(
              fontSize: 12,
              fontWeight: FontWeight.w400,
              color: color,
            ),
          ),
        ),
      ],
    );
  }
}

class _Description extends StatelessWidget {
  const _Description({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: const EdgeInsets.all(32),
      child: Text(
        'Lake Oeschinen lies at the foot of the Blüemlisalp in the Bernese '
        'Alps. Situated 1,578 meters above sea level, it is one of the '
        'larger Alpine Lakes. A gondola ride from Kandersteg, followed by a '
        'half-hour walk through pastures and pine forest, leads you to the '
        'lake, which warms to 20 degrees Celsius in the summer. Activities '
        'enjoyed here include rowing, and riding the summer toboggan run.',
        softWrap: true,
      ),
    );
  }
}
```

你不觉得这更易读吗？

# 🤔 有什么好处？

我理解为什么教程不经常这样做：它需要更多行（在我的示例中为 100 行），人们可能想知道为什么我们要创建这么多其他小部件。由于教程旨在专注于一个概念，因此这样编写它们可能会适得其反。但结果是，新采用者可能倾向于在他们的`build`方法中放置一个大的小部件树。
让我们看看为布局的每个部分都有一个独特的小部件有什么好处：

## 可读性

我们为布局的每个语义部分创建一个小部件。因此，每个小部件都有一个较小的`build`方法。它更易于阅读，因为您无需滚动即可到达小部件的末尾。

## 可理解性

每个小部件都有一个与其角色匹配的名称，这称为**语义命名**。通过这样做，当我们阅读代码时，更容易在我们的脑海中映射代码的哪一部分与我们在应用程序上看到的内容相匹配。我在这里看到了可理解性方面的两个改进：
\1. 当我们阅读其他地方引用的此类小部件时，我们几乎知道它的作用，而无需查看其实现。
2.在阅读带有语义命名的小部件的构建方法之前，我们已经对其内容有一个大致的了解。

## 可维护性

如果您必须更换一个组件或更改一个部件，它只会在一个地方，与其他小部件的其余部分分开。多亏了这种做法，它更不容易出错，因为每个小部件的角色都得到了很好的定义。在您的应用程序甚至另一个应用程序中的另一个页面中共享布局的一部分也将更加容易。

## Performances

前面的所有原因应该足以让您采用这种方式来创建 Flutter 应用程序，但是这样做还有一个好处：我们提高了应用程序的性能，因为每个小部件都可以与其他小部件分开重建（事实并非如此如果我们使用方法来分隔我们的布局部分）。例如，假设我们必须在单击它时增加红星旁边的数字。在这个版本中，我们可以制作`_Likes`一个`StatefulWidget`并处理这里的增量。当用户点击星星时，只有`_Likes`小部件会被重建。在第一个版本中，`MyApp`如果我们将其设为`StatefulWidget`.

[Flutter 文档中](https://flutter.dev/docs/perf/rendering/best-practices#controlling-build-cost)也解释了这种最佳实践：

> 当`setState()`在状态上调用时，所有后代小部件都将重建。因此，将`setState()`调用本地化到 UI 实际需要更改的子树部分。如果更改包含在树的一小部分，请避免在树的高处调用 setState()。

另一个优点是能够`const`更频繁地使用关键字。然后可以缓存和重新使用小部件。正如[Flutter 文档](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html#performance-considerations)所述：

> 重用小部件比创建新的（但配置相同的）小部件要高效得多。

# ⚡️ 如何提高工作效率？

如您所见，通过为布局的每个语义部分创建一个小部件，我们编写了更多代码。我们可以在 Visual Studio Code 中使用[Dart](https://marketplace.visualstudio.com/items?itemName=Dart-Code.dart-code)扩展提供的`stless`和`stful`片段，

为了我自己的需要，我创建了新的片段，称为`sless`和`sful`，这样我的工作效率比以往任何时候都高。如果您希望在 Visual Studio Code 中使用它们，则必须遵循此[文档](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_create-your-own-snippets)并添加以下内容：

```dart
{
  "Flutter stateless widget": {
		"scope": "dart",
		"prefix": "sless",
		"description": "Insert a StatelessWidget",
		"body": [
			"class $1 extends StatelessWidget {",
			"  const $1({",
			"    Key key,",
			"  }) : super(key: key);",
			"",
			"  @override",
			"  Widget build(BuildContext context) {",
			"    return Container(",
			"      $2",
			"    );",
			"  }",
			"}"
		]
	},
	"Flutter stateful widget": {
		"scope": "dart",
		"prefix": "sful",
		"description": "Insert a StatefulWidget",
		"body": [
			"class $1 extends StatefulWidget {",
			"  const $1({",
			"    Key key,",
			"  }) : super(key: key);",
			"",
			"  @override",
			"  _$1State createState() => _$1State();",
			"}",
			"",
			"class _$1State extends State<$1> {",
			"  @override",
			"  Widget build(BuildContext context) {",
			"    return Container(",
			"      $2",
			"    );",
			"  }",
			"}"
		]
	},
}
```

# 📕 结论

我相信这是编写 Flutter 应用程序的好方法，我希望你也相信。如果不是这样，我对你的意见很感兴趣😉！

从现在开始，记住这句话：“**Everything’s a widget but don’t put everything in one widget!**”。