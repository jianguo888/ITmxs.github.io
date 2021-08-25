---
title: "Getx"
date: 2021-08-19T23:10:11+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
 
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