---
title: "Flutter使用关键字进行延迟实例化"
date: 2021-09-06T15:03:58+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

随着 Dart 2.12 中 NNBD 的引入，创建了一个新关键字：`late`. 创建它的主要原因是允许非空字段，不必*立即*初始化。

```dart
// Allow this to be null for now, compiler trusts us, that we will assign it.
late final int i; 
MyConstructor(){
   i = getStartIndex(); // Compiler checks at this point, everything is ok!
   i = null; // This will cause an error, compiler is enforcing non-null
}
```

这是 Flutter 的必要功能/解决方法，因为 darts 要求使用`const`初始化程序，并且大多数 Flutter 开发人员现在可能已经熟悉它。在这篇文章中，我们将看看`late`!



## 让我们偷懒……

`late`还有另一个适用于 Flutter 代码的出色应用：**您可以删除许多`initState`/`constructor`调用！**

这是因为`late`“懒惰”运行，这意味着它在第一次被引用之前根本不会运行。这听起来可能不重要，但它对一般用例非常有用：**您的字段初始值设定项代码可以访问其他\*字段\*、\*方法\*和!`.\*this\*`**

这完全解锁了您的初始化程序代码，使其更加灵活。通常，您不能访问类实例上的其他字段或方法：

![image-20210906150517427](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210906150517427.png)

但是，在使用时`late`，这些都可以正常工作！

![image-20210906150529578](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210906150529578.png)

### 这与 Flutter 有什么关系？

以前，如果我们正在制作`AnimationController`，则必须在`initState`或 构造函数中完成，因为只能从方法中访问`this`所需的`vsync`。

```dart
AnimationController anim1;
 
@override
void initState() {
  super.initState();
  anim1 = AnimationController(vsync: this, duration: Duration(seconds: 1))..forward();
}
```

现在，我们可以这样写：

```dart
late AnimationController anim = AnimationController(vsync: this, duration: Duration(seconds: 1))..forward();
```

这为您节省了 6 行样板代码 🙂

您可以使用它从计算方法中获取初始值，或设置任何依赖于动态值的默认值。

您还可以创建构建器方法，并调用它们：

```dart
late AnimationController anim1 = createAnim(seconds: 1, play: true);
late AnimationController anim2 = createAnim(seconds: 2);
late AnimationController anim3 = createAnim(seconds: 3);
 
AnimationController createAnim({required int seconds, bool play = false}) {
  final c = AnimationController(vsync: this, duration: Duration(seconds: seconds));
  if(play) c.forward();
  c.addListener(() => setState((){}));
  return c;
}
```

不需要`initState`，不需要`@override`或`super()`样板，每一行都有意义。

### 小部件参数呢？

不幸的是，这不适用于默认构造函数参数，所以这样的事情仍然不起作用：

```dart
class Foo extends StatelessWidget {
  late final FocusNode focusNode; 
  // Compiler will complain this is not a const
  Foo({Key? key, this.focusNode = FocusNode()}) : super(key: key);
}
```

希望这种懒惰的实例化将来可以扩展到构造函数参数，但与此同时，这仍然是 dart 开发人员工具箱的一个非常好的补充。





深入理解空安全

https://dart.cn/null-safety/understanding-null-safety
