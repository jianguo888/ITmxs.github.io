---
title: "Getx在flutter中响应式设计"
date: 2021-08-24T23:09:53+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

## GetX 中有 3 种响应式方法

## 1.使用分离的构建器



![image-20210824231246225](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210824231246225.png)

## 2.使用构建器小部件



![image-20210824231138804](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210824231138804.png)

## 3.使用 if else 条件

![image-20210824231159298](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210824231159298.png)

# Bonus

您也可以更改断点



![image-20210824231322230](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210824231322230.png)

顺便说一下，GetX 的默认points是：

```
desktopChangePoint = 1200 
tabletChangePoint = 600 
watchChangePoint = 300
```

它超级简单而强大！





## 什么是 GetX 的状态管理解决方案？

GetX 包有 3 种管理状态的方法。

它们是反应式 (GetBuilder)、非反应式 (GetX、Obx) 和混合式 (MixinBuilder)。

)![image-20210824232410057](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210824232410057.png)

# Non-Reactive

## 获取生成器

`GetBuilder` 是一个简单的管理解决方案，仅在您需要时更新 UI。

## 基本用法

```dart
class Controller extends GetxController {
  int counter = 0;
  increase() {
    // Increases the counter
    counter++;
    // Updates the UI. If you don't use this method,
    // the counter will update in the backend but you won't see it on the UI
    update();
  }
}
GetBuilder<Controller>(
  init: Controller(), // you need to init controller only the first time
  builder: (c) {
      return ElevatedButton(
        child: Text('${c.counter}'),
        onPressed: c.increase,
    );
  },
);
```

## 生命周期方法

此外，您可以在控制器中使用生命周期方法。使用此属性，您将完全将业务层与表示层分开。

```dart
class Controller extends GetxController {
  // To initialize something for the controller.
  @override
  void onInit() {
    super.onInit();
  }
  
  // Called 1 frame after onInit(). It is the perfect place to enter
  // navigation events, like snackbar, dialogs, or a new route, or
  // async request.
  @override
  void onReady() {
    super.onReady();
  }
  
  // Dispose resources or closing events or streams before the controller destroyed.
  @override
  void onClose() {
    super.onClose();
  }
}
```

## 此外，您可以在`GetBuilder`或 中定义它们`GetX`。

```dart
GetBuilder<Controller>(
  initState: (_) => controller.fetchApi(),
  dispose: (_) => controller.closeStreams(),
  builder: (_) => Text('${controller.username}'),
),
```

# Reactive

## 什么是Reactive？

在 Reactive 中，您的数据取决于流。当数据更改时，所有侦听器都会立即更新。这意味着使用`GetX`或`Obx`，您不再需要使用`update()`方法。

## GetX

`GetX`是`GetBuilder`. 它基本上做同样的事情，但reactive方式。

## 基本用法

```dart
class Controller extends GetxController {
  // to define reactive data. Just add .obs property 
  final counter = 0.obs;
  increase() {
    // If  you want to reach the value of the counter just add .value property 
    counter.value++;
  }
}
GetX<Controller>(
  init: Controller(), // you need to init controller only the first time
  builder: (c) {
      return ElevatedButton(
        child: Text('${c.counter.value}'),
        onPressed: c.increase,
    );
  },
);
```

## 定义Reactive变量

其实很简单。只需将`.obs`属性添加到您的变量中，您就可以开始了

```dart
final name = ''.obs;
final isLogged = false.obs;
final count = 0.obs;
final balance = 0.0.obs;
final number = 0.obs;
final items = <String>[].obs;
final myMap = <String, int>{}.obs;
// Custom classes - it can be any class
final user = User().obs;
```

## 粒度更新

GetX 包还为我们提供了对正在更新的内容的精细更新控制。这意味着只会重建更改的小部件。其他小部件保持不变

```dart
import 'package:get/get.dart';
class Controller extends GetxController {
  final num1 = 0.obs;
  final num2 = 0.obs;
  int get sum => num1.value + num2.value;
  increaseNum1() => num1.value++;
  increaseNum2() => num2.value++;
}
GetX<Controller>(
  builder: (c) {
    print("Number #1 build");
    return Text('${c.num1.value}');
  },
),
GetX<Controller>(
  builder: (c) {
    print("Number #2 build");
    return Text('${c.num2.value}');
  },
),
GetX<Controller>(
  builder: (c) {
    print("Sum build");
    return Text('${c.sum.value}');
  },
),
```

## 对象

`Obx`是`GetX`.

## 基本用法

```dart
class Controller extends GetxController {
  final counter = 0.obs;
  increase() {
    counter.value++;
  }
}
// Since Obx does not have an init method, you need to define it externally.
final c = Controller();
Obx(
  (c) {
    return ElevatedButton(
      child: Text('${c.counter.value}'),
      onPressed: c.increase,
    );
  },
);
```

## 您也可以像这样到达控制器

```dart
class HomePage extends GetView<Controller> {
  ...
  final data = controller.fetchApi();
}
```

## GetBuilder 与 GetX 与 Obx

实际上，他们都在做同样的工作。您可以使用一个到另一个

`GetBuilder`仅在需要时更新，`GetX`并`Obx`在数据更改时更新。

之间唯一的区别`GetX`，并`Obx`是`GetX`具有类似的生命周期`init`，并且`dispose`，但是`Obx`没有。

哪一个适合你的口味，随它去吧。