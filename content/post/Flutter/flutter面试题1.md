---
title: "Flutter面试题1"
date: 2021-08-20T23:18:42+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍我对flutter的想法和理解

<!--more-->

我只是想分享我对flutter的想法和理解。我从维基百科、Stack Overflow 和其他一些媒体文章中获取了所有这些内容。基本上，我以我自己喜欢的顺序/格式包装所有这些内容。相信这篇文章会帮助你对flutter有一个基本的认识，可以用来在准备面试的时候回忆一下你的flutter知识。

## **什么是Flutter？**

Flutter是一个开放源码的UI 由谷歌创建的开发套件。它用于从单个代码库开发适用于 Android、iOS、Linux、Mac、Windows 和 Web 的应用程序。Flutter 应用程序是用 Dart 语言编写的。

## 什么是Dart？

Dart 是一种客户端优化的编程语言，适用于多个平台上的应用程序。它由 Google 开发，用于构建移动、桌面、服务器和 Web 应用程序。Dart 是一种面向对象、基于类、垃圾收集的语言，具有 C 风格的语法。Dart 可以编译为原生代码或 JavaScript。

## 特定于设计的widget

Flutter 框架包含两组符合特定设计语言的widget。

- **Material Design**widget实现了 Google 的同名设计语言。
- **Cupertino widget**实现了 Apple 的 iOS 人机界面指南

## Flutter/Dart 命名约定

- [UpperCamelCase](https://dart.dev/guides/language/effective-dart/style) - 类、枚举类型、typedef、扩展和类型参数。
- [lowerCamelCase](https://dart.dev/guides/language/effective-dart/style) - 标识符、常量和方法。
- [lowercase_with_underscores](https://dart.dev/guides/language/effective-dart/style) - 库、包、目录和源文件。

## Flutter Doctor

```
flutter doctor
```

此命令会检查您的环境并显示 Flutter 安装状态报告。此命令的输出显示您可能需要安装或执行更多任务的软件列表。

## Flutter Clean

```
flutter clean
```

此命令会清除项目的构建缓存。

## flutter 中的 pubspec.yaml 文件是什么？

[pubspec.yaml](https://dart.dev/tools/pub/pubspec)是一个文件，您可以在其中提供 Flutter 项目的所有依赖项和资产（插件、Dart 版本、字体、图像）。它也是您配置项目名称和描述的地方。

## Flutter pub 命令

- **flutter pub get** - 此命令获取当前工作目录中 pubspec.yaml 文件中列出的所有依赖项，以及它们的传递依赖项。如果系统缓存尚未包含依赖项，则 pub get 更新缓存，并在必要时下载依赖项。要将包映射回系统缓存，此命令会创建一个 .packages 文件。
- **flutter pub upgrade** - 此命令获取当前工作目录中 pubspec.yaml 文件中列出的所有依赖项的最新版本，以及它们的传递依赖项。

## 什么是widget？

Flutter 中的一切都是一个widget，但究竟什么是widget？widget是用户界面元素。

## Flutter 中有多少种类型的widget？

widget要么是有状态的，要么是无状态的。

- 无状态widget
- 有状态widget

## 有状态和无状态widget有什么区别？

- **无状态widget**不需要可变状态，即它是不可变的。无状态widget的状态只创建一次——因此，构建方法也只调用一次。简而言之，无状态widget在应用程序运行期间无法更改其状态，这意味着在应用程序运行时无法重绘widget。如果要重绘无状态widget，则需要创建widget的新实例。
- **有状态widget**具有可变状态，即它们是可变的并且可以在其生命周期内多次绘制。它们是可以多次更改状态的widget，并且可以在应用程序运行时在屏幕上多次重绘。

## State 是什么意思？什么是 setState()？

状态是widget的变量值/属性的集合。任何可以改变的东西，比如计数器计数、文本等，都可以成为 State 的一部分。

setState() 本质上是一种告诉应用程序使用新值/属性刷新和重建屏幕的方法。

状态对象由框架创建。为了更改您的widget，您需要更新状态对象，这可以使用可用于有状态widget的 setState() 函数来完成。setState() 设置状态对象的属性，进而触发 UI 的更新。但是这种技术有其自身的缺点。

## “StatefulWidget”的生命周期是什么？

- `createState()`— a 中的一个方法会`StatefulWidget`立即被调用，并且应该`State`为widget返回一个对象
- `initState() `—`State`创建widget后在对象中调用的第一个方法
- `didChangeDependencies()`—`initState()`在第一次构建widget后立即调用
- `build() `— 与`StatelessWidget`. 它在 之后立即调用`didChangeDependencies()`。每次 UI 需要渲染并返回widget的树时都会调用它。
- `didUpdateWidget()` — 当父widget发生变化并需要重绘其 UI 时调用它
- `deactivate()`— 调用 before `dispose()`，当这个对象从树中移除时
- `dispose() `— 在释放整个widget及其状态时调用。

## 热重载、热重启和完全重启有什么区别？

Flutter 的热重载功能可帮助您快速轻松地进行实验、构建 UI、添加功能和修复错误。热重载的工作原理是将更新的源代码文件注入正在运行的 Dart 虚拟机 (VM)。VM 用新版本的字段和函数更新类后，Flutter 框架会自动重建widget树，让您可以快速查看更改的效果。

- **热重载**将代码更改加载到 VM 中并重新构建widget树，保留应用程序状态；它不会重新运行 main() 或 initState()。热重载比热重启花费的时间更少。
- **热重启**将代码更改加载到 VM 中，并重新启动 Flutter 应用程序，从而丢失应用程序状态。热重启比热重载花费的时间长得多。
- **完全重新**启动会**重新**启动 iOS、Android 或 Web 应用程序。这需要更长的时间，因为它还会重新编译 Java/Kotlin/ObjC/Swift 代码。在 Web 上，它还会重新启动 Dart 开发编译器。

## 创建导航有哪些不同的方法？

Flutter中创建导航的三种方式

- **直接导航**与`MaterialPageRoute`

```
Route route = MaterialPageRoute(builder: (context) => NextScreen());
Navigator.push(context, route);
```

- 带有map route的**静态导航**- 您可以在 MaterialApp 中创建routes map，然后使用其名称push它

*在 MaterialApp 中创建*routes map*

```
void main() {
  runApp(
    MaterialApp(
      initialRoute: '/home',
      routes: <String, WidgetBuilder>{
        '/home': (context) => HomePage(),
        '/second': (context) => SecondHome(),
      },
    )
  );
}
```

*使用它的名字*Pushing* 

```
Navigator.pushNamed(context, '/second');
```

- 生成路由的**动态导航**——即在 MaterialApp 类中实现 onGenerateRoute 回调

```
MaterialApp(
  home: SecondPage(),
  onGenerateRoute: (RouteSettings settings) {
    switch (settings.name) {
      case '/home':
        return MaterialPageRoute(builder: (context)=> HomePage());
        break;
      case '/second':
        return MaterialPageRoute(builder: (context)=> SecondPage());
        break;
    }
  },
),
```

## Flutter 的构建模式

Flutter 工具支持三种模式

- 当您想使用热重载时，请在开发期间使用**调试模式**。**debug mode**
- 当您要分析性能时，请使用**配置文件模式**。 **profile mode**
- 当您准备好发布您的应用程序时，请使用**发布模式**。 **profile mode**

## 调试模式

- 热重载仅适用于调试模式。
- 模拟器和模拟器仅在调试模式下执行。
- 应用程序性能在调试模式下可能会很糟糕。
- 断言已启用。
- 服务扩展已启用。
- 编译针对快速开发和运行周期进行了优化（但不是针对执行速度、二进制大小或部署）。
- 调试开启，支持源码级调试的工具（如DevTools）可以连接到进程。

## **Profile mode**

- 在配置文件模式下，保留了一些调试功能——足以配置应用程序的性能。
- 模拟器和模拟器上禁用了配置文件模式，因为它们的行为不代表实际性能。
- 在移动设备上，配置文件模式类似于发布模式。
- 启用了一些服务扩展，例如启用性能覆盖的服务扩展。
- 启用跟踪，支持源级调试的工具（如 DevTools）可以连接到进程。

## Release Mode

- 使用发布模式部署应用程序，当你想要最大
- 优化和最小的占地面积。
- 断言被禁用。
- 调试信息被剥离。
- 调试被禁用。
- 编译针对快速启动、快速执行和小包大小进行了优化。
- 服务扩展被禁用。

## Dart 函数中的参数类型

一个函数可以有两种类型的参数：*required*和*optional*。

- Dart 的可选参数是*可选*的，因为调用者在调用函数时不需要为参数指定值。
- 可选参数可以是命名的或位置的，但不能两者兼而有之。
- 可选参数只能在任何必需参数之后声明。
- 可选参数可以有一个默认值，在调用者没有指定值时使用。
- 您可以从[这里](https://stackoverflow.com/questions/13264230/what-is-the-difference-between-named-and-positional-parameters-in-dart#:~:text=Dart has two types of optional parameters%3A named and positional.&text=Dart's optional parameters are optional,declared after any required parameters)获得更好的clarity 

## **Future class in dart**

Future 用于表示将来某个时间可用的潜在值或错误。

Future 的接收者可以注册回调，一旦它可用，就可以处理该值或错误。

## 什么是future？

Future 是 Future 类的一个实例。Future 表示异步操作的结果。它可以有两种状态：*未完成*或*已完成*。

- **未完成**——当你调用一个异步函数时，它返回一个未完成的未来。那个未来正在等待函数的异步操作完成或抛出错误。
- **已完成**— 如果异步操作成功，则未来完成并带有一个值。否则，它将以错误结束。

## 同步编程

在同步编程中，代码将逐行执行。

- *同步操作*——同步操作阻止其他操作执行，直到它完成。
- *同步函数*——同步函数只执行同步操作。

## 异步编程

异步操作让您的程序在等待另一个操作完成的同时完成工作。

- *异步操作*- 一旦启动，异步操作允许其他操作在完成之前执行。
- *异步函数*——异步函数至少执行一个异步操作，也可以执行同步操作。

一个`async`函数同步运行直到第一个`await`关键字。这意味着在`async`函数体内，第一个`await`关键字之前的所有同步代码都会立即执行。

## **什么是异步和等待？**

async 和 await 关键字提供了一种声明方式来定义异步函数并使用它们的结果。

- **async** — 您可以`async`在函数体之前使用关键字将其标记为异步。
- **await** — 您可以使用`await`关键字来获取异步表达式的完整结果。该`await`关键字仅在`async`函数内起作用。

## asnyc 与异步*？

- **async**给你一个`Future`——你将 async 关键字添加到一个函数中，该函数执行一些可能需要很长时间的工作。它返回包装在 Future 中的结果。
- **async\***给你一个`Stream`- 你添加 async* 关键字来创建一个函数，一次返回一堆未来值。结果被包装在一个流中。

## Dart 中的 Null 感知运算符

dart 中的空感知运算符允许您根据值是否为空进行计算。它是较长表达式的简写。您可以从[这里](https://medium.com/@thinkdigitalsoftware/null-aware-operators-in-dart-53ffb8ae80bb)获得更好的clarity。

- **??**

```
//assign y to x, unless y is null. Otherwise, assign z.
x = y ?? z
```

- **??=**

```
//如果 x 为空，则将 y 分配给 x。
x ??= y
```

- **?.**

```
// 仅当 x 不为空时才调用 foo()。
x?.foo()
```

## Spread operator

您可以使用展开运算符 ( `...`) 将一个列表的所有值插入到另一个列表中。

```
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

## **Null-aware spread operator**

如果展开运算符右侧的表达式可能为空，则可以使用可识别空值的展开运算符 ( `...?`)来避免异常。

```
var list;
var list2 = [0, ...?list];
assert(list2.length == 1);
```

## **Collection if**

您可以使用条件来构建集合。

```
var nav = [
  'Home',
  'Furniture',
  'Plants',
  if (promoActive) 'Outlet'
];
```

## **Collection for**

您可以使用重复来构建集合。

```
var listOfInts = [1, 2, 3];
var listOfStrings = [
  '#0',
  for (var i in listOfInts) '#$i'
];
```

## dynamic — var — final

- **dynamic **——可以改变变量的类型，也可以改变变量的值。
- **var** — 不能改变变量的类型，但可以改变变量的值。
- **final** — 不能在代码中更改变量的类型，也不能更改变量的值。

```
dynamic v = 123;// v 是 int 类型。
v = 456；// 将 v 的值从 123 更改为 456。
v = 'abc'; // 将 v 的类型从 int 更改为 String。无功 v = 123; // v 是 int 类型。
v = 456；// 将 v 的值从 123 更改为 456。
v = 'abc'; // 错误：无法将 v 的类型从 int 更改为 String。最终 v = 123；// v 是 int 类型。
v = 456；// 错误：无法将 v 的值从 123 更改为 456。
v = 'abc'; // 错误：无法将 v 的类型从 int 更改为 String。
```

## Flutter的优点

- 跨平台开发
- 更快的开发
- 好社区
- 实时和热重载
- 原生性能
- 提供原生的外观和感觉



- 如何动态更新 ListView？

- ChangeNotifier 和 notifyListeners 在 Provider 中的作用是什么？
- 什么是 ValueListenableBuilder？
- 列举几个 Flutter 团队开发的插件？
- Dart AOT 是如何工作的？
- Dart中var和dynamic类型的区别？
- Dart中'const'和'final'关键字之间的区别？
- TextEditingController 的重要性是什么？
- 为什么我们在 Listview 中使用“Reverse”属性？
- Modal 和 Persistent BottomSheet 的区别举个例子？
- Inherited Widget 与 Provider 有何不同？
- 为了使 CrossAxisAlignment.baseline 工作，我们需要设置的另一个属性是什么？
- 我们什么时候应该使用resizeToAvoidBottomInset？

# 谢谢…

## 我的参考











# 构建生命周期管理器来管理您的服务

最近提出的问题是当应用程序进入后台时如何停止服务。这篇文章将向您展示如何以可扩展的方式在一个地方处理所有这些。我们将使用 WidgetsBindingObserver 来监听`AppLifecycleState`和调用我们服务的停止/启动。我们将首先创建管理器并用它包装我们的应用程序。创建一个名为lifecycle_manager.dart 的新文件

```
class LifeCycleManager extends StatefulWidget {
  final Widget child;
  LifeCycleManager({Key key, this.child}) : super(key: key);
  _LifeCycleManagerState createState() => _LifeCycleManagerState();
}
class _LifeCycleManagerState extends State<LifeCycleManager>
    with WidgetsBindingObserver {
  @override
  void initState() {
    WidgetsBinding.instance.addObserver(this);
    super.initState();
  }
  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    print('state = $state');
  }
  @override
  Widget build(BuildContext context) {
    return Container(
      child: widget.child,
    );
  }
}
```

然后在主文件中，我们可以`MaterialApp`用新的小部件包装我们的。

```
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return LifeCycleManager(
      child: MaterialApp(
        title: 'Flutter Demo',
        home: HomeView(),
      ),
    );
  }
}
```

如果您运行它并最小化您的应用程序，您将在控制台中看到打印出的状态日志。

```
state = AppLifecycleState.inactive
state = AppLifecycleState.paused
state = AppLifecycleState.inactive
state = AppLifecycleState.resumed
```

我们将使用它来决定调用我们的服务的内容。为了给订阅流的服务提供一个通用接口，我们将创建一个这些服务必须实现的 StoppableService 抽象类。我们将向基类添加一些功能以跟踪它是否已停止，以便我们也可以利用它。

```
abstract class StoppableService {
  bool _serviceStoped = false;
  bool get serviceStopped => _serviceStoped;
  @mustCallSuper
  void stop() {
    _serviceStoped = true;
  }
  @mustCallSuper
  void start() {
    _serviceStoped = false;
  }
}
```

这个的实现看起来像这样。

```
class LocationService extends StoppableService {
  @override
  void start() {
    super.start();
    // start subscription again
  }
  @override
  void stop() {
    super.stop();
    // cancel stream subscription
  }
}
```

我们缺乏 Flutter 中反射的好处，因此我们必须在 LifeCycleManager 中跟踪这些服务并循环它们以停止和启动它们。在`LifeCycleManager`我们将保留一个列表`StoppableServices`并在生命周期函数调用中循环它们。

```
class _LifeCycleManagerState extends State<LifeCycleManager>
    with WidgetsBindingObserver {
  List<StoppableService> services = [
    locator<LocationService>(),
  ];
  ...
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    services.forEach((service) {
      if (state == AppLifecycleState.resumed) {
        service.start();
      } else {
        service.stop();
      }
    });
  }
}
```

如果您使用的架构类似于我[在这里展示](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide)的架构，则现在可以将其添加到您的应用程序中。在基于生命周期事件取消/重新订阅时，它为您提供了一个管理所有服务的单一位置。在此处查看我的一些其他[教程](https://www.filledstacks.com/tutorials)和[片段](https://www.filledstacks.com/snippets)。



明天见



https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/





我啦啦啦https://www.filledstacks.com/snippets





https://medium.com/geekculture/writing-custom-widgets-in-flutter-part-3-b-simpleoverlay-no-helpers-9ddf45cea983



github https://github.com/MatrixDev/Flutter-CustomWidgets/tree/main/lib