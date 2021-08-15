---
title: "Flutter中的bloc"
date: 2021-08-15T13:37:29+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

**在这篇文章中，我们将通过一个例子来看看如何使用 flutter_bloc 包在 Flutter 中使用 Bloc。**

**包链接：**[**flutter_bloc | Flutter 包 (pub.dev)**](https://pub.dev/packages/flutter_bloc)

![img](https://miro.medium.com/max/1400/0*-6V0mn8A-nG3INtT.jpg)



所以 Bloc 基本上使用了 Inherited Widget 的概念。我们可以使用 Stream 和 Sink 来实现 Bloc，但是，我们已经有一个包**flutter_bloc**，它是 Stream 和 Sink 的包装器。

在我们开始 bloc 之前，有两个概念需要明确：

- **事件**：事件基本上是应用程序的功能。假设对于我们的基本 Counter 应用程序，***Increment\***是一项功能，因此我们的事件之一是***Increment\***。
- 状态**：**状态是可以在小部件构建时同步读取的信息，并且在小部件的生命周期内可能会发生变化。或者，简单地说，我们可以说状态是我们的应用程序在事件发生之前和之后所存在的东西。***因此，在我们的 Counter 示例中，增量之前和之后的应用程序是状态。此外，我们可以说我们在递增时有一个状态。\***

![img](https://miro.medium.com/max/1400/1*zX8briMwYZ7dymAakD0u0Q.png)

在 BLOC 中，我们将**Event**作为BLOC 的**输入**。然后我们**在 Bloc 中**进行**处理**/**业务逻辑**并提供**State**作为**输出**。

在 BLOC  中，将有 3 个主要文件：

- **bloc 文件：**该文件包含主要的业务逻辑
- **事件文件：**此文件说明您的应用程序中存在的所有事件。
- **状态文件：**此文件包含您的应用程序经历的所有状态。

# 让我们从编码开始吧！

**我们将使用 BLOC 重写基本的计数器应用程序！**

**第1步：导入**[**flutter_bloc**](https://pub.dev/packages/flutter_bloc)**在pubspec.yaml**

```
flutter_bloc:
```

**第 2 步：**创建一个名为的文件夹`bloc`并在其中创建 2 个文件。在这里，我们将它们命名为`counter_bloc`and `counter_event`。由于我们没有多个状态，因此我们不会创建`counter_state`.

![img](https://miro.medium.com/max/720/1*UUxqsfm1qnEsqfx7cf_Vxw.png)

这就是项目结构的样子。您可以根据自己的习惯命名文件。

**第 3 步：**让我们首先在没有 Bloc 的情况下从头开始创建我们应用程序的基本 UI 和功能。这是 main.dart 和 home.dart 文件：
**main.dart：**

```dart
import 'package:bloc_counter_example/home.dart';
import 'package:flutter/material.dart';
void main() {
  runApp(MyApp());
}
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: MyHomePage(),
    );
  }
}
```

**home.dart**

```dart
import 'package:flutter/material.dart';
class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}
class _MyHomePageState extends State<MyHomePage> {
  int counter = 0;
@override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => setState(() {
          counter++;
        }),
      ),
      appBar: AppBar(
        title: Text('Bloc Counter Example'),
      ),
      body: Center(
        child: Text(
          '$counter',
          style: TextStyle(fontSize: 50.0),
        ),
      ),
    );
  }
}
```

所以这就是我们从头开始创建的基本应用程序（Counter Application）。现在，让我们处理我们的**BLOC。**

**第 4 步：**让我们创建我们的事件。由于这是一个简单的例子，我们只有 1 个事件，即**增量**，我们甚至不需要传递任何参数。所以我们将`enum`在这种情况下使用。所以，我们`counter_event.dart`将看起来像这样：

```
enum CounterEvent { increment }
```

**第 5 步：**现在，让我们处理我们的 bloc 文件，即`counter_bloc.dart`.
在这个文件中，我们需要指定我们的业务逻辑。我们必须创建一个扩展 Bloc 类的类。让我向您展示它的外观：

```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'counter_event.dart';
class CounterBloc extends Bloc<CounterEvent, int> {
  CounterBloc() : super(0);
@override
  Stream<int> mapEventToState(CounterEvent event) async* {
    switch (event) {
      case CounterEvent.increment:
        yield state + 1;
        break;
      default:
        break;
    }
  }
}
```

所以，上面的代码就是我们的`counter_bloc.dart`. 这是我们应用程序的主要核心，它将增加计数器。让我们对这段代码理解：

- **class CounterBloc extends Bloc<CounterEvent, int>**
  这里，**CounterEvent**是我们的事件即枚举我们在创建`counter_event.dart`和**INT**是状态，因为我们简单地递增的整数变量。如果您的应用程序很复杂，而不是 int，您可以指定您的 State 类。
- **CounterBloc() : super(0);**
  所以，这基本上是 CounterBloc() 类的构造函数，在我们的例子中，我们将一个初始值（即 0）传递给父类，即 Bloc 类，因为我们的 CounterBloc 扩展了 Bloc。
- 然后我们需要实现一个名为 的方法`mapEventToState`。该方法基本上将我们的输入事件与相应的输出状态映射。
  **Stream<int> mapEventToState(CounterEvent event) async\*
  **此方法将事件作为输入并返回状态流，因此我们需要使其成为异步。现在，如果您有自定义状态文件，则必须将**int**替换为您的状态类。
- 现在，我们检查事件。如果是CounterEvent.increment，我们需要增加这个值。`yield`向周围`async*`函数的输出流添加一个值。就像`return`，但不会终止该功能。

```dart
switch (event) {
      case CounterEvent.increment:
        yield state + 1;
        break;
      default:
        break;
    }
```

**到这里，我们完成了我们的**`**counter_bloc.dart**`**文件实现！**

所以，我们的 BLOC 准备好了！现在我们只需要将它与我们的 UI ie`main.dart`和`home.dart`文件集成

我希望到这里已经很清楚了！ 😉

**步骤 6：**打开`main.dart`文件并使用 BlocProvider 包装 MaterialApp 的孩子。您的文件将如下所示：

```
import 'package:bloc_counter_example/bloc/counter_bloc.dart';
import 'package:bloc_counter_example/home.dart';
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
void main() {
  runApp(MyApp());
}
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: BlocProvider<CounterBloc>(
        create: (context) => CounterBloc(),
        child: MyHomePage(),
      ),
    );
  }
}
```

因此，基本上 BlocProvider 将提供对整个 Widget Tree 的 Bloc 的访问。

```dart
BlocProvider<CounterBloc>(
        create: (context) => CounterBloc(),
        child: MyHomePage(),
      ),
```

这里， CounterBloc 是我们在上一步中创建的 bloc 类。我们需要提供要创建的块（这里是 CounterBloc()）以及应该可以访问该块的子块。是的！这就是`main.dart`文件。现在让我们转到`home.dart`😍

**第 7 步：**打开`home.dart`文件并创建一个实例`**CounterBloc()**`

```dart
CounterBloc _counterBloc;
```

现在，在我们的`build`方法中，让我们实例化我们的`_counterBloc`变量。

```dart
_counterBloc = BlocProvider.of<CounterBloc>(context);
```

上面的语句是做什么的？？？它实际上使您可以访问我们创建的 CounterBloc。现在，使用`_counterBloc`我们可以访问我们的事件和状态。

**第 8 步：**现在，将 Text() 小部件（显示计数的地方）与`**BlocBuilder**`

```dart
body: Center(
        child: BlocBuilder<CounterBloc, int>(
          builder: (context, state) {
            return Text(
              '$state',
              style: TextStyle(fontSize: 50.0),
            );
          },
        ),
      ),
```

在这里，`state`我们可以看到的变量可以访问状态。在这种情况下，状态只是一个将递增的整数值。所以我们可以直接显示状态。现在，让我们更改浮动操作按钮的 onPressed() 方法！

**第 9 步：**在 onPressed for Floating Action Button 中，我们需要将事件添加到 bloc。句法：

```dart
onPressed: () => _counterBloc.add(CounterEvent.increment),
```

**这是您的**`**home.dart**`**文件现在的外观：**

```dart
import 'package:bloc_counter_example/bloc/counter_bloc.dart';
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'bloc/counter_event.dart';
class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}
class _MyHomePageState extends State<MyHomePage> {
  CounterBloc _counterBloc;
@override
  void dispose() {
    _counterBloc.close();
    super.dispose();
  }
@override
  Widget build(BuildContext context) {
    _counterBloc = BlocProvider.of<CounterBloc>(context);
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _counterBloc.add(CounterEvent.increment),
      ),
      appBar: AppBar(
        title: Text('Bloc Counter Example'),
      ),
      body: Center(
        child: BlocBuilder<CounterBloc, int>(
          builder: (context, state) {
            return Text(
              '$state',
              style: TextStyle(fontSize: 50.0),
            );
          },
        ),
      ),
    );
  }
}
```



我们刚刚在我们的基本计数器应用程序中实现了 Bloc 💙

在下一篇文章中，我们将采用具有多个状态的复杂应用程序并使用 Bloc 实现它。

