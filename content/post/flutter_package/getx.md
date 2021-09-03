---
title: "Getx"
date: 2021-08-19T23:10:11+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
 
---

> 本文主要介绍

<!--more-->

https://github.com/Bunny1376/pageView/tree/master/lib

https://nastaran1376mohammadi.medium.com/how-to-use-getx-flutter-package-to-create-a-pageview-66f9b6a175b9





# 让我们在 Flutter 中使用 GetX

https://medium.com/mindful-engineering/lets-getx-in-flutter-4eaff2826ac7

*在本文中，我们将研究****GetX\****及其****原理、特性\****以及如何**在我们的 Flutter 应用程序中****使用\****它。*

# 介绍

**GetX**是一个快速、轻量级且功能强大的微框架，使用它，我们可以轻松地管理状态、路由并以高效的方式执行依赖注入。

Flutter 本身就是一个快速发展的框架，它更新了很多东西，它是插件，作为开发人员，我们需要让我们的应用程序保持最新，并在 pubspec.dart 文件中为各种事情提供最新的依赖项，而不是许多这样的依赖项，我们可以使用**GetX**，它都在一个包中，它提供了很多正确的东西，这反过来又使我们能够防止我们在应用程序开发中经常面临的依赖噩梦。

如果我们使用**GetX，**那么我们可以避免使用StatefulWidgets，它肯定可以减少一些RAM消耗，不需要与initState()等其他方法混淆，而是我们有更清晰的方法来做到这一点。我们有控制器，它有自己的生命周期，可以将业务逻辑与视图分开，每当使用控制器发生任何更改时，它都会自动更新视图。



![img](https://miro.medium.com/max/875/0*G-tKPOnuxEX70J2w.jpeg)

GetX 有 3 个基本**原则。**让我们看看他们，

- **性能：GetX**主要关注更好的性能和最少的资源使用，所以在我看来，它是最好的状态管理技术。
- **生产力**：除了一流的性能外，**GetX**还具有易于记忆的语法。它节省了开发人员的大量时间，并通过减少资源的使用来提高应用程序的速度。
- **组织：GetX**允许我们取消视图上的业务逻辑，并将依赖注入和导航与 UI 分开。您不需要上下文来在屏幕之间导航。你不需要通过多提供者将你的控制器/模型/块类注入你的小部件树，因为这个 GetX 使用它自己的依赖注入功能。

# 安装：将此包用作库

将 Get 添加到包的 pubspec.yaml 文件中：



![img](https://miro.medium.com/max/851/0*ZbdHbg6HOijWKy2E.png)

将其导入到将要使用的文件中：



![img](https://miro.medium.com/max/861/0*QAQ7WeYAquK4tw28.png)

# GetX 的特点

## 状态管理

*Flutter 中*有很多可用的状态管理库，如 B *LoC、Provider、MobX*等。**GetX**本身为我们提供了**两种**状态管理选项。

- *简单的状态管理器（称为****GetBuilder\****）。
- *反应状态管理器（称为****GetX/Obx\****）。

**简单的状态管理：GetX**具有 GetBuilder，用于使 UI 屏幕或视图与控制器上的变量和方法进行交互。从这里，我们可以调用函数并监听状态的变化。所以，让我们先创建一个控制器。

```dart
import 'package:get/get.dart';

class CounterController extends GetxController {
  var counter = 0;
  increment() { 
    counter++;
    update();
  }
}
```

我们使用**update()**方法，以便 UI 中的小部件可以监听我们在控制器方法中所做的更改。现在要聆听更改，我们必须用**GetBuilder**包装我们的小部件。



```dart

GetBuilder<CounterController>(
      init: CounterController(),
      builder: (value) {
        return Text(
          '${value.counter}',
          style: Theme.of(context).textTheme.headline4,
        );
      },
    );
```



我们只需要在第一次和第二次为同一个控制器使用 ReBuilder 时初始化我们的控制器，不要再次初始化它。

> 无需将 StatefulWidget 与 GetX 一起使用，因为**GetBuilder**替换了**StatefulWidget**，我们可以将所有小部件保持为无状态，并根据我们的要求使用 GetBuilder 包装特定小部件。

**反应性状态管理：** GetBuilder 不是反应性的，这意味着我们将无法为此使用流，我们必须使用**GetX**类。语法**的getX**非常相似**GetBuilder**但是这种方法是基于扑的**流**。**GetX**具有所有必需的**响应****式**编程功能，我们不需要为每个变量使用 StreamControllers、StreamBuilders 和每个状态的类，甚至我们不需要使用任何由代码生成器自动生成的类。来看看怎么用吧



```dart
import 'package:get/get.dart';

class CounterController extends GetxController {
  var counter = 0.obs;
  increment() => counter++;
}
```



我们必须将**.obs**添加到任何变量以使其可观察。基本上我们使用 counter 作为 int 类型的流。

我们可以使用**GetX 的**力量从我们的角度聆听计数器的变化。像这样，



```dart
GetX<CounterController>(
      init: CounterController(),
      builder: (value) {
        return Text(
          '${value.counter}',
          style: Theme.of(context).textTheme.headline4,
        );
      },
    );
```



我们可以使用**Obx**小部件而不是**GetX**，它为我们提供匿名函数，为我们创建一个小部件，并从我们的视图中使用我们的变量和方法，我们需要首先像这样初始化控制器，



```dart
final CounterController controller = Get.put(CounterController());

Obx(
   () => Text(
     '${controller.counter}',
      style: Theme.of(context).textTheme.headline4,
    ),
 );
```



## 路线管理

如果我们想使用像 Snackbar、Bottomsheets、对话框等小部件，那么我们可以使用**GetX**，因为它可以在不使用上下文的情况下构建这些小部件，并且它提供了用于在 Flutter 应用程序中导航的 API，只需简单且所需的代码更少。

## 以下是我们如何使用 GetX 导航到屏幕……

要导航到新屏幕，我们可以像这样使用它，



```dart

Get.to(() => NewScreen())
```

![img](https://miro.medium.com/max/450/0*ueRlM8k6fQ3SmNVE.png)

要使用名称导航到新屏幕，我们可以像这样使用它，



```dart
Get.toNamed('profile')
```



关闭快餐栏、对话框、底页或任何你通常会用 Navigator.pop(context) 关闭的东西；我们可以这样使用它



```dart

Get.back()
```



要导航到下一个屏幕并且不想返回上一个屏幕（用于 SplashScreens、登录屏幕等），我们可以像这样使用它，



```dart

Get.off(NewScreen())
```



要导航到下一个屏幕并取消所有以前的路线（在购物车应用程序、民意调查和测试中很有用），我们可以这样使用它，



```dart
Get.offAll(NewScreen())
```



# 依赖管理

**GetX**为我们的**Flutter**应用程序提供了一个简单而强大的依赖管理器，我们可以使用它在一行代码中使用类名访问 Bloc 或 Controller。

![img](https://miro.medium.com/max/256/0*W2i2NLJAmY0Y8SjG.png)

**Get.put() 是**插入依赖项的最常见方式，它立即加载并且可以直接使用。我们可以这样使用它



```dart
Controller controller = Get.put(Controller()); //
```



**Get.lazyPut()**延迟加载依赖项，以便仅在使用时实例化它。我们可以这样使用它



```dart

Get.lazyPut<Controller>(() => Controller());
```



**Get.lazyPut()**只加载一次依赖项，这意味着如果我们再次删除并创建路由，它不会再次加载依赖项，因此为了克服这种默认行为，我们必须使用**fenix**属性。



```dart

Get.lazyPut<Controller>(() => Controller(), fenix: true);
```



当路线回到导航堆栈中时，依赖项再次重新初始化。

**Get.putAsync()**用于注册异步实例。我们可以这样使用它



```dart
Get.putAsync<Controller>( () async => await Controller() );
```



当我们需要在整个应用程序中保持该实例处于活动状态时，我们可以将 true 设置为可选的**永久**属性。



```dart

Get.putAsync<Controller>( () async => await Controller(), permanent: true );
```



**Get.create()**当我们每次都必须创建依赖项的新实例时使用。我们可以这样使用它



```dart
Get.create(() => Controller());
```



## 国际化

国际化是开发人员利用系统提供的 API 执行的一项活动，使应用程序的阿拉伯语或中文与英语一样好。

![img](https://miro.medium.com/max/275/0*3XL98QcN-YHpmukj.gif)

**GetX**提供了作为简单键值字典映射的翻译工具。要创建我们自己的自定义类，我们必须使用 Translations 类扩展我们的类。我们可以这样使用它



```dart

import 'package:get/get.dart';

class Messages extends Translations {
  @override
  Map<String, Map<String, String>> get keys =>
      {
        'en_US': {
          'hello': 'Hello World',
        },
        'de_DE': {
          'hello': 'Hallo Welt',
        }
      };
}
```



要使用它，我们只需将**.tr**附加到指定的键，它就会被翻译，使用**Get.locale**和**Get.fallbackLocale**的当前值**传入****GetMaterialApp**的参数**。**



```dart

Text('hello'.tr);
```



## 更改主题

当我们使用**GetX 时，**我们不需要使用任何比“GetMaterialApp”小部件更高级别的小部件，而且我们也不需要使用“ThemeProvider”小部件来更改主题。



```dart

Get.changeTheme(ThemeData.dark());
```



将主题从亮到暗或从暗到亮切换，我们可以使用这样的东西，



```dart

Get.changeTheme(Get.isDarkMode ? ThemeData.light() : ThemeData.dark());
```



当**Get.isDarkMode**被激活时，它将切换到浅色主题，反之亦然。

## **满意点1：简洁易懂**

如您所见，此文件夹结构非常干净且易于理解，因为它没有太多复杂性。所以即使是初学者也可以轻松查看代码结构。此外，这种简单的项目结构有助于我们快速跟踪错误并解决它们。

## 对第 2 点的满意度：独立于其他功能

如您所见，每个功能都有自己的文件夹，因此它们彼此完全独立。

## 对第 3 点的满意度：可扩展

假设现在您希望在我们的聊天应用程序中添加任何其他功能，例如，您现在希望拥有一个聊天列表或您朋友的所有联系人列表，因此您只需创建一个名为`chat_list`or的新文件夹`contact_list`并创建相同的文件夹结构。因此，您可以说这个项目结构是可扩展的，因为添加新功能不需要您更改现有的功能文件。

https://segmentfault.com/a/1190000039139198

https://www.cxyzjd.com/article/zl18603543572/115586290

https://blog.csdn.net/qq_36407748/article/details/115204260

https://ithelp.ithome.com.tw/articles/10215918





# Obx响应式状态管理

### 介绍

> 响应式编程可能会让很多人感到陌生，因为它很复杂，但是GetX将响应式编程变得非常简单。
>
> - 你不需要创建StreamControllers.
> - 你不需要为每个变量创建一个StreamBuilder。
> - 你不需要为每个状态创建一个类。
> - 你不需要为一个初始值创建一个get。
>
> 使用 Get 的响应式编程就像使用 setState 一样简单。

### 定义Obx变量的三种方式

```dart
// 第一种 使用 Rx{Type}
// final name = RxString('');
// final isLogged = RxBool(false);
// final count = RxInt(0);
// final balance = RxDouble(0.0);
// final items = RxList<String>([]);
// final myMap = RxMap<String, int>({});

// 第二种是使用 Rx，规定泛型 Rx<Type>。
// final name = Rx<String>('');
// final isLogged = Rx<Bool>(false);
// final count = Rx<Int>(0);
// final balance = Rx<Double>(0.0);
// final number = Rx<Num>(0)
// final items = Rx<List<String>>([]);
// final myMap = Rx<Map<String, int>>({});
// 自定义类 - 可以是任何类
// final user = Rx<User>();

// 第三种更实用、更简单、更可取的方法，只需添加 .obs 作为value的属性。
// final name = ''.obs;
// final isLogged = false.obs;
// final count = 0.obs;
// final balance = 0.0.obs;
// final number = 0.obs;
// final items = <String>[].obs;
// final myMap = <String, int>{}.obs;
// 自定义类 - 可以是任何类
// final user = User().obs;
```

### 计数器案例

#### 第一步：应用程序入口设置

```dart
import 'package:flutter/material.dart';
import 'package:flutter_getx_example/ObxCountExample/ObxCountExample.dart';
import 'package:get/get.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      title: "GetX",
      home: ObxCountExample(),
    );
  }
}
```

#### 第二步：声明Rx变量以及改变计数器的方法

```dart
RxInt count = RxInt(0);
// var count = Rx<double>(0);
// var count = 0.obs;

void increment() {
  count++;
}
```

#### 第三步：使用Obx监听值的改变

```dart
Obx(() => Text(
  "count的值为: $count",
  style: TextStyle(color: Colors.red, fontSize: 30),
)),
```

#### 完整代码

```dart
import 'dart:ffi';

import 'package:flutter/material.dart';
import 'package:get/get.dart';

class ObxCountExample extends StatelessWidget {
  RxInt count = RxInt(0);
  // var count = Rx<double>(0);
  // var count = 0.obs;

  void increment() {
    count++;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("GetX Obx---计数器"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            Obx(() => Text(
              "count的值为: $count",
              style: TextStyle(color: Colors.red, fontSize: 30),
            )),
            SizedBox(height: 20,),
            ElevatedButton(
              onPressed: () {
                increment();
              },
              child: Text("点我加1"))
          ],
        ),
      ),
    );
  }
}
```

#### 效果展示

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/obx---count.gif)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/obx---count.gif)

### 自定义类案例

#### 第一步：应用程序入口设置

```dart
import 'package:flutter/material.dart';
import 'package:flutter_getx_example/ObxCustomClassExample/ObxCustomClassExample.dart';
import 'package:get/get.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      title: "GetX",
      home: ObxCustomClassExample(),
    );
  }
}
```

#### 第二步：创建Teacher类

```dart
import 'package:get/get.dart';

class Teacher {

  // rx 变量
  var name = "Jimi".obs;
  var age = 18.obs;

  // 构造函数创建
  // var name;
  // var age;
  // Teacher({this.name, this.age});
}
```

#### 第三步：监听Teacher状态改变

```dart
import 'package:flutter/material.dart';
import 'package:flutter_getx_example/ObxCustomClassExample/Teacher.dart';
import 'package:get/get.dart';

class ObxCustomClassExample extends StatelessWidget {

  var teacher = Teacher();

  // final teacher = Teacher(name: "Jimi", age: 18).obs;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("GetX Obx---自定义类"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            Obx(() => Text(
              "我的名字是 ${teacher.name.value}",
              style: TextStyle(color: Colors.red, fontSize: 30),
            )),
            SizedBox(height: 20,),
            ElevatedButton(
              onPressed: () {
                teacher.name.value = teacher.name.value.toUpperCase();

                // teacher.update((val) {
                //   teacher.value.name = teacher.value.name.toString().toUpperCase();
                // });
              },
              child: Text("转换为大写"))
          ],
        ),
      ),
    );
  }
}
```

#### 效果展示

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/getx_obx_customclass.gif)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/getx_obx_customclass.gif)

## 总结

通过这个章节知道了`GetX`是一个高性能的状态管理、智能的依赖注入和便捷的路由管理，我们介绍了什么`GetX`以及如果使用，包括`Snackbar`、`Dialog`、`BottomSheet`、`Navigation`、`Obx` 等进行了一个简单的介绍，使用`GetX`后能极简的缩减我们的代码。

# Flutter GetX---RxList、Rx([])、.obs对比分析

## 前言

首先我们知道`GetX`组件里面`obs`状态管理有三种创建属性的方式，我们这里以`List`为例

- Rx([])
- RxList
- .obs



## 三种方式对比分析

我们声明了一个类ListController`继承自`GetxController`，用于属性创建以及状态通知的方法，首先我们用三种方式来创建属性并且通过`convertToUpperCase`方法进行对值的改变，然后我们通过调用`update()`方法来进行数据更新，最后我们使用该属性状态的值，接下来我们看一下三种使用方式的对比。

- 第一种Rx([])
- 第二种RxList
- 第三种 .obs

```dart
import 'dart:convert';
import 'package:get/get.dart';

class ListController extends GetxController {
  // 第一种
  final listOne = Rx<List<Map>>([
    {
      "name": "Jimi",
      "age": 18
    }
  ]);

  // 第二种
  final listTwo = RxList([
    {
      "name": "Jimi",
      "age": 18
    }
  ]);

  // 第三种
  final listThree = [{
    "name": "Jimi",
    "age": 18
  }].obs;

  void convertToUpperCase() {
    listOne.value[0]["name"] = listOne.value.first["name"].toUpperCase();
    listTwo.toList().first["name"] = listTwo.toList().first["name"].toString().toUpperCase();
    listThree.toList().first["name"] = listTwo.toList().first["name"].toString().toUpperCase();
    update();
  }
}
```

我们在页面中获取状态更新的值

```dart
import 'package:flutter/material.dart';
import 'package:flutter_getx_dvanced_example/ListController.dart';
import 'package:get/get.dart';


void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {

  ListController listController = Get.put(ListController());

  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      title: "GetX",
      home: Scaffold(
        appBar: AppBar(
          title: Text("GetX"),
        ),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            crossAxisAlignment: CrossAxisAlignment.center,
            children: [
              GetBuilder<ListController>(
                init: listController,
                builder: (controller) {
                  return Text(
                    "我的名字是 ${controller.listOne.value.first['name']}",
                    style: TextStyle(color: Colors.orange, fontSize: 30),
                  );
                },
              ),
              SizedBox(height: 20,),
              GetBuilder<ListController>(
                init: listController,
                builder: (controller) {
                  return Text(
                    "我的名字是 ${controller.listTwo.first['name']}",
                    style: TextStyle(color: Colors.green, fontSize: 30),
                  );
                },
              ),
              SizedBox(height: 20,),
              GetBuilder<ListController>(
                init: listController,
                builder: (controller) {
                  return Text(
                    "我的名字是 ${controller.listThree.first['name']}",
                    style: TextStyle(color: Colors.green, fontSize: 30),
                  );
                },
              ),
              SizedBox(height: 20,),
              ElevatedButton(
                onPressed: () {
                  listController.convertToUpperCase();
                },
                child: Text("转换为大写"))
            ],
          ),
        ),
      ),
    );
  }
}
```

效果展示



![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/getx_dvanced%E4%B8%89%E7%A7%8D%E5%B1%9E%E6%80%A7%E5%88%9B%E5%BB%BA%E6%96%B9%E5%BC%8F%E5%AF%B9%E6%AF%94%E5%88%86%E6%9E%90.gif)

`Rx<T>` 继承自`_RxImpl<T>`，`_RxImpl<T>`又继承`RxNotifier<T>`并混合 `RxObjectMixin<T>` 类

`RxImpl<T>`它主要的作用是管理泛型的所有逻辑的。

`RxObjectMixin<T>` 它主要的作用是管理注册到`GetX`和`Obx`的全局对象，比如`Widget`的`Rx`值

`Rx<T>`它主要的作用是将自定义模型类用Rx`来进行包装，

```dart
class Rx<T> extends _RxImpl<T> {
  Rx(T initial) : super(initial);

  @override
  dynamic toJson() {
    try {
      return (value as dynamic)?.toJson();
    } on Exception catch (_) {
      throw '$T has not method [toJson]';
    }
  }
}

abstract class _RxImpl<T> extends RxNotifier<T> with RxObjectMixin<T> {
  _RxImpl(T initial) {
    _value = initial;
  }

  void addError(Object error, [StackTrace? stackTrace]) {
    subject.addError(error, stackTrace);
  }

  Stream<R> map<R>(R mapper(T? data)) => stream.map(mapper);

  void update(void fn(T? val)) {
    fn(_value);
    subject.add(_value);
  }

  void trigger(T v) {
    var firstRebuild = this.firstRebuild;
    value = v;
    if (!firstRebuild) {
      subject.add(v);
    }
  }
}
```

## RxList源码分析

```
RxList<E>`继承自`ListMixin<E>`实现了`RxInterface<List<E>>`并混合了`NotifyManager<List<E>>, RxObjectMixin<List<E>>
```

`RxList<E>`它的主要作用是创建一个类似于`List<T>` 的一个列表

```dart
class RxList<E> extends ListMixin<E>
  with NotifyManager<List<E>>, RxObjectMixin<List<E>>
  implements RxInterface<List<E>> {
  RxList([List<E> initial = const []]) {
    _value = List.from(initial);
  }

  factory RxList.filled(int length, E fill, {bool growable = false}) {
    return RxList(List.filled(length, fill, growable: growable));
  }

  factory RxList.empty({bool growable = false}) {
    return RxList(List.empty(growable: growable));
  }

  /// Creates a list containing all [elements].
  factory RxList.from(Iterable elements, {bool growable = true}) {
    return RxList(List.from(elements, growable: growable));
  }

  /// Creates a list from [elements].
  factory RxList.of(Iterable<E> elements, {bool growable = true}) {
    return RxList(List.of(elements, growable: growable));
  }

  /// Generates a list of values.
  factory RxList.generate(int length, E generator(int index),
                          {bool growable = true}) {
    return RxList(List.generate(length, generator, growable: growable));
  }

  /// Creates an unmodifiable list containing all [elements].
  factory RxList.unmodifiable(Iterable elements) {
    return RxList(List.unmodifiable(elements));
  }

  @override
  Iterator<E> get iterator => value.iterator;

  @override
  void operator []=(int index, E val) {
    _value[index] = val;
    refresh();
  }

  /// Special override to push() element(s) in a reactive way
  /// inside the List,
  @override
  RxList<E> operator +(Iterable<E> val) {
    addAll(val);
    refresh();
    return this;
  }

  @override
  E operator [](int index) {
    return value[index];
  }

  @override
  void add(E item) {
    _value.add(item);
    refresh();
  }

  @override
  void addAll(Iterable<E> item) {
    _value.addAll(item);
    refresh();
  }

  @override
  int get length => value.length;

  @override
  @protected
  List<E> get value {
    RxInterface.proxy?.addListener(subject);
    return _value;
  }

  @override
  set length(int newLength) {
    _value.length = newLength;
    refresh();
  }

  @override
  void insertAll(int index, Iterable<E> iterable) {
    _value.insertAll(index, iterable);
    refresh();
  }

  @override
  Iterable<E> get reversed => value.reversed;

  @override
  Iterable<E> where(bool Function(E) test) {
    return value.where(test);
  }

  @override
  Iterable<T> whereType<T>() {
    return value.whereType<T>();
  }

  @override
  void sort([int compare(E a, E b)?]) {
    _value.sort(compare);
    refresh();
  }
}
```

## .obs源码分析

当我们在调用`.obs`的时候其实内部的实现源码还是通过`RxList<e>(this)`进行了一层包装，设计这个主要的目的就是为了方便开发者进行使用

```dart
extension ListExtension<E> on List<E> {
  RxList<E> get obs => RxList<E>(this);

  /// Add [item] to [List<E>] only if [item] is not null.
  void addNonNull(E item) {
    if (item != null) add(item);
  }

  // /// Add [Iterable<E>] to [List<E>] only if [Iterable<E>] is not null.
  // void addAllNonNull(Iterable<E> item) {
  //   if (item != null) addAll(item);
  // }

  /// Add [item] to List<E> only if [condition] is true.
  void addIf(dynamic condition, E item) {
    if (condition is Condition) condition = condition();
    if (condition is bool && condition) add(item);
  }

  /// Adds [Iterable<E>] to [List<E>] only if [condition] is true.
  void addAllIf(dynamic condition, Iterable<E> items) {
    if (condition is Condition) condition = condition();
    if (condition is bool && condition) addAll(items);
  }

  /// Replaces all existing items of this list with [item]
  void assign(E item) {
    // if (this is RxList) {
    //   (this as RxList)._value;
    // }

    clear();
    add(item);
  }

  /// Replaces all existing items of this list with [items]
  void assignAll(Iterable<E> items) {
    // if (this is RxList) {
    //   (this as RxList)._value;
    // }
    clear();
    addAll(items);
  }
}
```

## 总结

我们对`Rx<T>([])`、`RxList<E>`、`.obs`进行了一个总结，在我们平时的开发过程中建议大家使用`.obs`即可，因为这是最简单的方式。