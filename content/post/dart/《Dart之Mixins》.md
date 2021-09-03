---
title: "《Dart之Mixins》"
subtitle: ""
date: 2021-07-31T21:04:40+08:00
lastmod: 2021-07-31T21:04:40+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍关于 Dart mixin 的一些理解。理解 mixin 概念的关键在于理解中间类。

<!--more-->





> Mixins are a way of reusing code in multiple class hierarchies

先来看一个简单例子：

```
class Piloted {
  int astronauts = 1;
  void describeCrew() {
    print('Number of astronauts: $astronauts');
  }
}

class PilotedCraft extends Spacecraft with Piloted {
  // ···
}
```

`PilotedCraft` 拥有 `astronauts` 字段和 `describeCrew()` 方法。

# mixin 是什么？

维基百科中这样定义 mixin：

> In object-oriented programming languages, a Mixin is a class that contains methods for use by other classes without having to be the parent class of those other classes.

即，mixin 是另外一个普通类，我们可以在不继承这个类的情况下从这个类”借用”方法和变量。

> Support for the mixin keyword was introduced in Dart 2.1. Code in earlier releases usually used abstract class instead.

从这个角度来讲，mixin 不过是 `abstract class`。

> Java tries to make up for this by using Interfaces, but that is not as useful or flexible as mixins.

从这个角度来讲，可以认为 mixin 是带实现的接口。

## 小节

- mixin 有点类似 `abstract class`
- mixin 有点类似 `interface`
- 不能继承 mixin
- 可以使用 `mixin`, `abstract class`, `class` 来作为 mixin

# 如何使用 mixin?

使用 mixin 的方法很简单：`with` 关键字后面跟上 mixin 的名字即可。

```
class Musician extends Performer with Musical {
  // ···
}

class Maestro extends Person
    with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
```

实现 mixin 的方法同样也很简单：创建一个继承自 `Object` 的类并且不要声明构造方法。如果想让 mixin 作为普通类使用，使用 `class` 关键字；如果不想让 mixin 作为普通类使用，使用 `mixin` 关键字代替 `class`。

```
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

# on 的用法

> The keyword on is used to restrict our mixin’s use to only classes which either extends or implements the class it is declared on. In order to use the on keyword, you must declare your mixin using the mixin keyword

```
class B {}
mixin Y on B {
  void hi() {
    print('hi');
  }
}
class Q with Y {}
```

则有如下错误提示：

```
Error: 'Object' doesn't implement 'B' so it can't be used with 'Y'.
```

`on` 关键字限制了 `Y` 的使用范围：`Y` 只能用于继承或实现了 `B` 的类。修复方式是让 `Q` 继承自 `B`：

```
class Q extends B with Y {}
```

# mixin 解决了什么问题？

mixin 解决了多重继承中的 [Deadly Diamond of Death(DDD)](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem) 问题。

多重继承问题简单描述。各个类的继承关系如下：

![mixin 与继承 -w500](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/23/15768441684217.jpg)

```
class Performer {
    abstract void perform();
}

class Dancer extends Performer {
    void perform() {}
}

class Singer extends Performer {
    void perform() {}
}

class Musician extends Dancer, Singer {
}
```

问题来了，当调用 `Musician.perform()` 时，到底会**调用哪个 `perform()` 方法是模糊的**。

来看 mixin 如何解决这个问题。见 [Dart for Flutter : Mixins in Dart - Flutter Community - Medium](https://medium.com/flutter-community/https-medium-com-shubhamhackzz-dart-for-flutter-mixins-in-dart-f8bb10a3d341)

```
class Performer {
    abstract void perform();
}

mixin Dancer {
    void perform() {}
}

mixin Singer {
    void perform() {}
}

class Musician extends Performer with Dancer, Singer {
}
```

现在，当调用 `Musician.perform()` 时，到底会**调用哪个 `perform()` 方法是确定的**。在这里是调用 `Singer.perform()`。

mixin 有一套明确的机制来选择调用哪个方法。

![minx 是线性的栈结构 -w500](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/23/15768445983681.jpg)

假设 `Musician` 类使用多个 mixin (`Dancer`, `Singer`)。该类有个方法名为 `perform()`，`Musician` 类继承自 `Performer` 类。

- 首先，将 `Performer` 类置于栈顶
- 其次，后声明的 mixin 优先于后声明的 mixin。按顺序将 mixin 置于栈中，在这里分别是 `Dancer`, `Singer`
- 最后，将 `Musician` 类自己置于栈中。`Musician` 类中的 `perform()` 被优先调用

------

Dart 使用的是单重继承 (Java 也是单重继承，C++ 是多重继承)。多重继承更为强大，但会引起 [Deadly Diamond of Death(DDD)](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem) 问题。

Java 使用接口(interface)来部分实现多重继承。多重继承的问题是需要在每个类中实现接口(interface)，所以并不是一个好的方案。(实际上 Java 已经通过默认方法修复了这个问题)

所以 Dart 中就有了 mixin。

# 理解 mixin

> Mixins in Dart work by creating a new class that layers the implementation of the mixin on top of a superclass to create a new class — it is not “on the side” but “on top” of the superclass, so there is no ambiguity in how to resolve lookups.
> Mixins is not a way to get multiple inheritance in the classical sense. Mixins is a way to abstract and reuse a family of operations and state. It is similar to the reuse you get from extending a class, but it is compatible with single-inheritance because it is linear.
> [StackOverflow](https://stackoverflow.com/a/45903671)

```
class A {
  String getMessage() => 'A';
}

class B {
  String getMessage() => 'B';
}

class P {
  String getMessage() => 'P';
}

class AB extends P with A, B {}

class BA extends P with B, A {}

void main() {
  String result = '';

  AB ab = AB();
  result += ab.getMessage();

  BA ba = BA();
  result += ba.getMessage();

  print(result);
}
```

以上这段代码输出 `BA`。

从语义上讲以上这段代码等同于：

```
class PA = P with A;
class PAB = PA with B;

class AB extends PAB {}

class PB = P with B;
class PBA = PB with A;

class BA extends PBA {}
```

继承结构图是这样的：

![mixin 的继承关系 -w400](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/23/15770742264332.jpg)

> Since each mixin application creates a new class, it also creates a new interface (because all Dart classes also define interfaces). As described, the new class extends the superclass and includes copies of the mixin class members, but it also implements the mixin class interface.
> In most cases, there is no way to refer to that mixin-application class or its interface; the class for Super with Mixin is just an anonymous superclass of the class declared like class C extends Super with Mixin {}. If you name a mixin application like class CSuper = Super with Mixin {}, then you can refer to the mixin application class and its interface, and it will be a sub-type of both Super and Mixin.

理解 mixin 的关键在于它是线性的。

# 使用场景

- 在没有共同父类的各个类中共享代码时
- 在父类中实现某种方法无意义时

# 源码分析

(2020-7-22 更新：[从mixin机制理解Flutter App启动 - 掘金](https://juejin.im/post/5efbf499e51d4534b979050c) 也是分析 Flutter App 启动过程时无法理解 mixin，然后逐步了解 mixin 概念，再回过头来看 Flutter App 启动过程)

`runApp()` 是我们运行应用的入口。这个方法的代码如下：

```
void runApp(Widget app) {
  WidgetsFlutterBinding.ensureInitialized()
    ..attachRootWidget(app)
    ..scheduleWarmUpFrame();
}
```

这里的的 `WidgetsFlutterBinding` 联系着 Flutter framework 和 Flutter engine。

> A concrete binding for applications based on the Widgets framework.
>
> This is the glue that binds the framework to the Flutter engine.

`WidgetsFlutterBinding` 定义如下：

```
class WidgetsFlutterBinding extends BindingBase with GestureBinding, ServicesBinding, 
 SchedulerBinding, PaintingBinding, SemanticsBinding,
 RendererBinding, WidgetsBinding {
}

abstract class BindingBase {
}

mixin GestureBinding on BindingBase implements HitTestable, HitTestDispatcher, HitTestTarget {}

mixin ServicesBinding on BindingBase {}

mixin SchedulerBinding on BindingBase, ServicesBinding {}

mixin PaintingBinding on BindingBase, ServicesBinding {}

mixin SemanticsBinding on BindingBase {}

mixin RendererBinding on BindingBase, ServicesBinding, SchedulerBinding, GestureBinding, SemanticsBinding, HitTestable {}

abstract class HitTestable {}
```

- 定义 mixin。`mixin RendererBinding` 定义了一个名为 `RendererBinding` 的 mixin
- 限制 mixin 的使用范围。`mixin RendererBinding on BindingBase, ServicesBinding` 限制 `RendererBinding` 只能用于继承或实现了 `BindingBase` 或 `ServicesBinding` 的类上
- 使用 mixin。`class WidgetsFlutterBinding extends BindingBase with RendererBinding`

```
class WidgetsFlutterBinding extends BindingBase
    with
        GestureBinding,
        ServicesBinding,
        SchedulerBinding,
        PaintingBinding,
        SemanticsBinding,
        RendererBinding,
        WidgetsBinding {}

abstract class BindingBase {
  void initInstances() {
    print("BindingBase.initInstances()");
  }

  BindingBase() {
    initInstances();
  }
}

mixin WidgetsBinding
    on
        BindingBase,
        SchedulerBinding,
        GestureBinding,
        RendererBinding,
        SemanticsBinding {
  static WidgetsBinding _instance;

  @override
  void initInstances() {
    super.initInstances();
    _instance = this;
    print('WidgetsBinding.initInstances() $this');
  }
}

mixin GestureBinding on BindingBase implements HitTestable {
  static GestureBinding _instance;

  void initInstances() {
    super.initInstances();
    _instance = this;
    print('GestureBinding.initInstances() $this');
  }
}

mixin ServicesBinding on BindingBase {
  static ServicesBinding _instance;

  void initInstances() {
    super.initInstances();
    _instance = this;
    print('ServicesBinding.initInstances() $this');
  }
}

mixin SchedulerBinding on BindingBase, ServicesBinding {
  static SchedulerBinding _instance;

  void initInstances() {
    super.initInstances();
    _instance = this;
    print('SchedulerBinding.initInstances() $this');
  }
}

mixin PaintingBinding on BindingBase, ServicesBinding {
  static PaintingBinding _instance;

  void initInstances() {
    super.initInstances();
    _instance = this;
    print('PaintingBinding.initInstances() $this');
  }
}

mixin SemanticsBinding on BindingBase {
  static SemanticsBinding _instance;

  void initInstances() {
    super.initInstances();
    _instance = this;
    print('SemanticsBinding.initInstances() $this');
  }
}

mixin RendererBinding
    on
        BindingBase,
        ServicesBinding,
        SchedulerBinding,
        GestureBinding,
        SemanticsBinding,
        HitTestable {
  static RendererBinding _instance;

  void initInstances() {
    super.initInstances();
    _instance = this;
    print('RendererBinding.initInstances() $this');
  }
}

abstract class HitTestable {}

main(List<String> args) {
  WidgetsFlutterBinding b = WidgetsFlutterBinding();
  print('main(): $b');
}
```

这段代码输出为：

```
BindingBase.initInstances()
GestureBinding.initInstances() Instance of 'WidgetsFlutterBinding'
ServicesBinding.initInstances() Instance of 'WidgetsFlutterBinding'
SchedulerBinding.initInstances() Instance of 'WidgetsFlutterBinding'
PaintingBinding.initInstances() Instance of 'WidgetsFlutterBinding'
SemanticsBinding.initInstances() Instance of 'WidgetsFlutterBinding'
RendererBinding.initInstances() Instance of 'WidgetsFlutterBinding'
WidgetsBinding.initInstances() Instance of 'WidgetsFlutterBinding'
main(): Instance of 'WidgetsFlutterBinding'
```

可以这样理解，`WidgetsFlutterBinding` 并不是继承自 `BindingBase`，而是继承自如下一个匿名类：

```
class BindingBase with
        GestureBinding,
        ServicesBinding,
        SchedulerBinding,
        PaintingBinding,
        SemanticsBinding,
        RendererBinding,
        WidgetsBinding
```

# 参考

- [从mixin机制理解Flutter App启动 - 掘金](https://juejin.im/post/5efbf499e51d4534b979050c)
- [mixin specification](https://github.com/dart-lang/language/blob/master/accepted/2.1/super-mixins/feature-specification.md#dart-2-mixin-declarations) - 最权威
- [Dart: What are mixins?](https://medium.com/flutter-community/dart-what-are-mixins-3a72344011f3) - 最详细
- [参考](https://dart.dev/samples#mixins)
- [参考2](https://dart.dev/guides/language/language-tour#adding-features-to-a-class-mixins)
- [Dart for Flutter : Mixins in Dart](https://medium.com/flutter-community/https-medium-com-shubhamhackzz-dart-for-flutter-mixins-in-dart-f8bb10a3d341)
- [Introduction to Mixins in Dart ← Alligator.io](https://alligator.io/dart/mixins/) - 最浅显