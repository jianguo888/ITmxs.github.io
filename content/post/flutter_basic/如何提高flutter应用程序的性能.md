---
title: "如何提高flutter应用程序的性能"
date: 2021-09-08T23:22:41+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

关于如何提高 Flutter 应用程序的性能，存在许多疑问和问题。

我们必须澄清一下，Flutter 默认是高性能的，但我们在编写代码时必须避免犯一些错误，以使应用程序以出色且快速的方式运行。

通过我将给您的这些技巧，我们可以避免使用分析工具。

那么，让我们开始吧，但首先，让我们知道**您在构建应用程序时的主要困难**是**什么？**

[维护](https://codemagic.io/start/?utm_source=maintenance&utm_medium=main_difficulty&utm_campaign=blog_question)

[我没有 mac](https://codemagic.io/start/?utm_source=no_mac&utm_medium=main_difficulty&utm_campaign=blog_question)

[释放](https://codemagic.io/start/?utm_source=releasing&utm_medium=main_difficulty&utm_campaign=blog_question)

[保持团队循环](https://codemagic.io/start/?utm_source=team_in_loop&utm_medium=main_difficulty&utm_campaign=blog_question)

## 不要将小部件拆分为方法

当我们有一个很大的布局时，我们通常做的就是使用每个小部件的方法进行拆分。

以下示例是一个包含页眉、中心和页脚小部件的小部件。

```dart
class MyHomePage extends StatelessWidget {
  Widget _buildHeaderWidget() {
    final size = 40.0;
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: CircleAvatar(
        backgroundColor: Colors.grey[700],
        child: FlutterLogo(
          size: size,
        ),
        radius: size,
      ),
    );
  }

  Widget _buildMainWidget(BuildContext context) {
    return Expanded(
      child: Container(
        color: Colors.grey[700],
        child: Center(
          child: Text(
            'Hello Flutter',
            style: Theme.of(context).textTheme.display1,
          ),
        ),
      ),
    );
  }

  Widget _buildFooterWidget() {
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: Text('This is the footer '),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Padding(
        padding: const EdgeInsets.all(15.0),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            _buildHeaderWidget(),
            _buildMainWidget(context),
            _buildFooterWidget(),
          ],
        ),
      ),
    );
  }
}
```

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/giphy.gif)

我们之前看到的是一种反模式。内部发生的事情是，当我们对整个小部件进行更改和刷新时，它也会刷新我们在方法中拥有的小部件，这导致我们浪费 CPU 周期。

我们应该做的是通过以下方式将这些方法转换为无状态的Widget。

```dart
class MyHomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Padding(
        padding: const EdgeInsets.all(15.0),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            HeaderWidget(),
            MainWidget(),
            FooterWidget(),
          ],
        ),
      ),
    );
  }
}

class HeaderWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final size = 40.0;
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: CircleAvatar(
        backgroundColor: Colors.grey[700],
        child: FlutterLogo(
          size: size,
        ),
        radius: size,
      ),
    );
  }
}

class MainWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Expanded(
      child: Container(
        color: Colors.grey[700],
        child: Center(
          child: Text(
            'Hello Flutter',
            style: Theme.of(context).textTheme.display1,
          ),
        ),
      ),
    );
  }
}

class FooterWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: Text('This is the footer '),
    );
  }
}
```

有状态/无状态小部件具有特殊的“缓存”机制(基于键、小部件类型和属性)，仅在必要时重建。除此之外，这有助于我们封装和重构我们的小部件。(分而治之)

添加`const`到我们的小部件是一个好主意，我们稍后会看到为什么这很重要。

## 避免重复重建所有小部件

这是我们开始使用 Flutter 时犯的一个典型错误，我们学会了重建我们的`StatefulWidget`using `setState`.

下面的示例是一个小部件，它包含一个中心的 Square，带有一个 fav 按钮，每次按下该按钮都会导致颜色发生变化。哦，该页面还有一个带有背景图像的小部件。

此外，我们将在每个小部件的构建方法之后添加一些打印语句，以查看它是如何工作的。

```dart
class _MyHomePageState extends State<MyHomePage> {
  Color _currentColor = Colors.grey;

  Random _random = new Random();

  void _onPressed() {
    int randomNumber = _random.nextInt(30);
    setState(() {
      _currentColor = Colors.primaries[randomNumber % Colors.primaries.length];
    });
  }

  @override
  Widget build(BuildContext context) {
    print('building `MyHomePage`');
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        child: Icon(Icons.colorize),
      ),
      body: Stack(
        children: [
          Positioned.fill(
            child: BackgroundWidget(),
          ),
          Center(
            child: Container(
              height: 150,
              width: 150,
              color: _currentColor,
            ),
          ),
        ],
      ),
    );
  }
}

class BackgroundWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    print('building `BackgroundWidget`');
    return Image.network(
      'https://cdn.pixabay.com/photo/2017/08/30/01/05/milky-way-2695569_960_720.jpg',
      fit: BoxFit.cover,
    );
  }
}
```

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/giphy.gif)

您将看到打印了 2 个日志：

```
flutter: building `MyHomePage`
flutter: building `BackgroundWidget`
```

每次按下按钮时，我们都会刷新整个屏幕、Scaffold、Background 小部件，最后是对我们重要的容器。

随着我们了解更多，我们知道重建整个小部件不是一个好的做法，我们只需要重建我们想要更新的内容。

许多人知道这可以通过像`flutter_bloc`, `mobx`,`provider`等这样的状态管理包来完成。但很少有人知道它也可以在没有任何外部包的情况下完成，也就是说，使用 Flutter 框架已经提供的开箱即用的类。

我们现在将使用 `ValueNotifier.`

```dart
class _MyHomePageState extends State<MyHomePage> {
  final _colorNotifier = ValueNotifier<Color>(Colors.grey);
  Random _random = new Random();

  void _onPressed() {
    int randomNumber = _random.nextInt(30);
    _colorNotifier.value =
        Colors.primaries[randomNumber % Colors.primaries.length];
  }

  @override
  void dispose() {
    _colorNotifier.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    print('building `MyHomePage`');
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        child: Icon(Icons.colorize),
      ),
      body: Stack(
        children: [
          Positioned.fill(
            child: BackgroundWidget(),
          ),
          Center(
            child: ValueListenableBuilder(
              valueListenable: _colorNotifier,
              builder: (_, value, __) => Container(
                height: 150,
                width: 150,
                color: value,
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

现在您将看不到任何打印语句。

这很完美，我们只是重建我们需要的小部件。如果我们想要更好地分离业务逻辑和视图(并且可能在其中添加更多逻辑)，我们还可以使用另一个有趣的小部件，我们还可以在通知程序中处理更多数据。

现在使用相同的示例`ChangeNotifier`。

```dart
//------ ChangeNotifier class ----//
class MyColorNotifier extends ChangeNotifier {
  Color myColor = Colors.grey;
  Random _random = new Random();

  void changeColor() {
    int randomNumber = _random.nextInt(30);
    myColor = Colors.primaries[randomNumber % Colors.primaries.length];
    notifyListeners();
  }
}
//------ State class ----//

class _MyHomePageState extends State<MyHomePage> {
  final _colorNotifier = MyColorNotifier();

  void _onPressed() {
    _colorNotifier.changeColor();
  }

  @override
  void dispose() {
    _colorNotifier.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    print('building `MyHomePage`');
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        child: Icon(Icons.colorize),
      ),
      body: Stack(
        children: [
          Positioned.fill(
            child: BackgroundWidget(),
          ),
          Center(
            child: AnimatedBuilder(
              animation: _colorNotifier,
              builder: (_, __) => Container(
                height: 150,
                width: 150,
                color: _colorNotifier.myColor,
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

最重要的是，通过这最后 2 个小部件，我们可以避免不必要的重建。

## 尽可能使用 const 小部件

将关键字`const`用于我们可以在编译时初始化的常量是一种很好的做法。让我们也不要忘记`const`尽可能多地使用我们的小部件，这使我们能够捕获和重用小部件，以避免由其祖先引起的不必要的重建。

让我们使用 重用最后一个示例`setState`，但现在我们将添加一个计数器，每次按下 fav 按钮时，该计数器都会增加值。

```dart
class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _onPressed() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    print('building `MyHomePage`');
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        child: Icon(Icons.colorize),
      ),
      body: Stack(
        children: [
          Positioned.fill(
            child: BackgroundWidget(),
          ),
          Center(
              child: Text(
            _counter.toString(),
            style: Theme.of(context).textTheme.display4.apply(
                  color: Colors.white,
                  fontWeightDelta: 2,
                ),
          )),
        ],
      ),
    );
  }
}

class BackgroundWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    print('building `BackgroundWidget`');
    return Image.network(
      'https://cdn.pixabay.com/photo/2017/08/30/01/05/milky-way-2695569_960_720.jpg',
      fit: BoxFit.cover,
    );
  }
}
```

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/giphy.gif)

我们有 2 个打印语句，一个在主小部件的构建中，另一个在后台小部件中。每次我们按下 fab 按钮时，我们都会看到子部件也被重建，即使它有固定的内容。

```
flutter: building `MyHomePage`
flutter: building `BackgroundWidget`
```

现在`const`用于我们的小部件，代码如下所示：

```dart
class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _onPressed() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    print('building `MyHomePage`');
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        child: Icon(Icons.colorize),
      ),
      body: Stack(
        children: [
          Positioned.fill(
            child: const BackgroundWidget(),
          ),
          Center(
              child: Text(
            _counter.toString(),
            style: Theme.of(context).textTheme.display4.apply(
                  color: Colors.white,
                  fontWeightDelta: 2,
                ),
          )),
        ],
      ),
    );
  }
}

class BackgroundWidget extends StatelessWidget {
  const BackgroundWidget();

  @override
  Widget build(BuildContext context) {
    print('building `BackgroundWidget`');
    return Image.network(
      'https://cdn.pixabay.com/photo/2017/08/30/01/05/milky-way-2695569_960_720.jpg',
      fit: BoxFit.cover,
    );
  }
}
```

当我们在按下 fav 按钮时看到日志时，我们只能看到父小部件的初始打印(当然我们可以使用我在本技巧之前向您展示的技术改进这一点)，因此我们避免了标记为的小部件的重建常量

## 使用`itemExtent`ListView中的长列表

有时候当我们有一个很长的列表，想用scroll进行大幅度的跳转时， 的使用`itemExtent`很重要，我们来看一个简单的例子。

我们有一个包含 10,000 个元素的列表。按下按钮后，我们将跳转到最后一个元素。在这个例子中，我们不会使用`itemExtent`，我们会让孩子们决定它的大小。

```dart
class MyHomePage extends StatelessWidget {
  final widgets = List.generate(
    10000,
    (index) => Container(
      height: 200.0,
      color: Colors.primaries[index % Colors.primaries.length],
      child: ListTile(
        title: Text('Index: $index'),
      ),
    ),
  );

  final _scrollController = ScrollController();

  void _onPressed() async {
    _scrollController.jumpTo(
      _scrollController.position.maxScrollExtent,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        splashColor: Colors.red,
        child: Icon(Icons.slow_motion_video),
      ),
      body: ListView(
        controller: _scrollController,
        children: widgets,
      ),
    );
  }
}
```

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/giphy.gif)

如果您看到结果，则跳转非常缓慢(约 10 秒)。这是因为让孩子们决定自己的尺寸的成本。这甚至会阻塞用户界面！

为了避免这种情况，我们必须使用`itemExtent`属性，使用此滚动机制可以利用子项范围的预知来节省工作。

```dart
class MyHomePage extends StatelessWidget {
  final widgets = List.generate(
    10000,
    (index) => Container(
      color: Colors.primaries[index % Colors.primaries.length],
      child: ListTile(
        title: Text('Index: $index'),
      ),
    ),
  );

  final _scrollController = ScrollController();

  void _onPressed() async {
    _scrollController.jumpTo(
      _scrollController.position.maxScrollExtent,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        splashColor: Colors.red,
        child: Icon(Icons.slow_motion_video),
      ),
      body: ListView(
        controller: _scrollController,
        children: widgets,
        itemExtent: 200,
      ),
    );
  }
}
```

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/giphy.gif)

有了这个小小的改变，我们就可以立即跳跃，没有延迟。

## 避免在 AnimatedBuilder 中重建不必要的小部件

通常我们想为我们的小部件添加动画。然后我们通常做的是向我们添加一个侦听器`AnimationController`并调用`setState`. 但是正如我们在开始时看到的那样，这不是一个好的做法。相反，我们将使用`AnimatedBuilder`小部件仅重建我们想要制作动画的小部件。

我们将创建一个屏幕，其中包含一个中心显示当前计数的小部件，当按下 fav 按钮时，小部件会旋转 360。

```dart
class _MyHomePageState extends State<MyHomePage>
    with SingleTickerProviderStateMixin {
  AnimationController _controller;
  int counter = 0;

  void _onPressed() {
    setState(() {
      counter++;
    });
    _controller.forward(from: 0.0);
  }

  @override
  void initState() {
    _controller = AnimationController(
        vsync: this, duration: const Duration(milliseconds: 600));
    super.initState();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        splashColor: Colors.red,
        child: Icon(Icons.slow_motion_video),
      ),
      body: AnimatedBuilder(
        animation: _controller,
        builder: (_, child) => Transform(
          alignment: Alignment.center,
          transform: Matrix4.identity()
            ..setEntry(3, 2, 0.001)
            ..rotateY(360 * _controller.value * (pi / 180.0)),
          child: CounterWidget(
            counter: counter,
          ),
        ),
      ),
    );
  }
}

class CounterWidget extends StatelessWidget {
  final int counter;

  const CounterWidget({Key key, this.counter}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    print('building `CounterWidget`');
    return Center(
      child: Text(
        counter.toString(),
        style: Theme.of(context).textTheme.display4.apply(fontWeightDelta: 3),
      ),
    );
  }
}
```

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/giphy.gif)

完美，我们已经拥有了。现在让我们在计数器小部件中添加一个打印件，看看会发生什么，然后再试一次。

```
flutter: building `CounterWidget`
flutter: building `CounterWidget`
flutter: building `CounterWidget`
flutter: building `CounterWidget`
flutter: building `CounterWidget`
...
flutter: building `CounterWidget`
```

我们看到在旋转时它正在重建我们的小部件。但是，有很多打印语句，我们如何避免呢？(可以只重建一次，因为我们使用 setState 来刷新计数器)。

很简单，我们使用`child`提供的属性`AnimatedBuilder`，它允许我们缓存小部件以在我们的动画中重用它们。我们这样做是因为那个小部件不会改变，它唯一会做的就是旋转，但为此我们有`Transform`小部件。

```dart
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        splashColor: Colors.red,
        child: Icon(Icons.slow_motion_video),
      ),
      body: AnimatedBuilder(
        animation: _controller,
        child: CounterWidget(
          counter: counter,
        ),
        builder: (_, child) => Transform(
          alignment: Alignment.center,
          transform: Matrix4.identity()
            ..setEntry(3, 2, 0.001)
            ..rotateY(360 * _controller.value * (pi / 180.0)),
          child: child,
        ),
      ),
    );
  }
```

我们有相同的视觉结果，但如果我们看到日志，它不再重建我们的计数器小部件(只有一次因为我们更新了计数器)，我们优化了动画。

## 避免在动画中使用不透明度

我们将再次提及动画，因为很多时候我们通常会`Opacity`在其中使用小部件。

我们将重新创建之前的示例，但`Transform`我们将使用 Opacity 来使小部件消失并再次出现，而不是使用。

```dart
class _MyHomePageState extends State<MyHomePage>
    with SingleTickerProviderStateMixin {
  AnimationController _controller;
  int counter = 0;

  void _onPressed() {
    setState(() {
      counter++;
    });
    _controller.forward(from: 0.0);
  }

  @override
  void initState() {
    _controller = AnimationController(
        vsync: this, duration: const Duration(milliseconds: 600));
    _controller.addStatusListener((status) {
      if (status == AnimationStatus.completed) {
        _controller.reverse();
      }
    });
    super.initState();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        splashColor: Colors.red,
        child: Icon(Icons.slow_motion_video),
      ),
      body: AnimatedBuilder(
        animation: _controller,
        child: CounterWidget(
          counter: counter,
        ),
        builder: (_, child) => Opacity(
          opacity: (1 - _controller.value),
          child: child,
        ),
      ),
    );
  }
}
```

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/giphy.gif)

然而，正如我们看到的动画工作，动画`Opacity`小部件直接导致小部件(可能还有它的子树)重建每一帧，这不是很有效。

因此，为了改进和优化这一点，我们有两个选择：

### 1 - 使用 `FadeTransition`

```dart
class _MyHomePageState extends State<MyHomePage>
    with SingleTickerProviderStateMixin {
  AnimationController _controller;
  int counter = 0;

  void _onPressed() {
    setState(() {
      counter++;
    });
    _controller.forward(from: 0.0);
  }

  @override
  void initState() {
    _controller = AnimationController(
        vsync: this, duration: const Duration(milliseconds: 600));
    _controller.addStatusListener((status) {
      if (status == AnimationStatus.completed) {
        _controller.reverse();
      }
    });
    super.initState();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        splashColor: Colors.red,
        child: Icon(Icons.slow_motion_video),
      ),
      body: FadeTransition(
        opacity: Tween(begin: 1.0, end: 0.0).animate(_controller),
        child: CounterWidget(
          counter: counter,
        ),
      ),
    );
  }
}
```

### 2 - 使用 `AnimatedOpacity`

```dart
const duration = const Duration(milliseconds: 600);

class _MyHomePageState extends State<MyHomePage> {
  int counter = 0;
  double opacity = 1.0;

  void _onPressed() async {
    counter++;
    setState(() {
      opacity = 0.0;
    });
    await Future.delayed(duration);
    setState(() {
      opacity = 1.0;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: _onPressed,
        splashColor: Colors.red,
        child: Icon(Icons.slow_motion_video),
      ),
      body: AnimatedOpacity(
        opacity: opacity,
        duration: duration,
        child: CounterWidget(
          counter: counter,
        ),
      ),
    );
  }
}
```

这两个选项都比`Opacity`直接使用小部件更有效。

## 概括

正如我最初提到的，Flutter 足够强大，可以毫无问题地运行我们的应用程序，但遵循良好实践并尽可能优化我们的应用程序总是好的。

https://blog.codemagic.io/how-to-improve-the-performance-of-your-flutter-app./#dont-split-your-widgets-into-methods