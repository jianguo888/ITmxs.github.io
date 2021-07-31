---
title: "《Dart入门笔记》"
subtitle: ""
date: 2021-07-31T20:51:30+08:00
lastmod: 2021-07-31T20:51:30+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["dart"]
categories: ["dart"]

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



> 本文主要介绍Dart 基础知识笔记。

<!--more-->





# tour

- `main()` 函数是 Dart 程序的入口
- `main()` 函数返回 `void` 并具有可选的 `List<String>` 参数作为参数
- 所有对象都从 `Object` 类继承
- Dart 是强类型
- 当您想明确地不希望有任何类型时，使用特殊类型 `dynamic`
- Dart 可以在函数内创建函数（ 嵌套 函数或局部函数 ），可以创建匿名函数
- Dart 支持顶级变量
- 标识符以下划线（_）开头时表示私有成员变量
- Dart 支持类型推断，`var name = 'Bob';` 这里的 name 类型推断为 String
- 未初始化的变量的初始值为 `null`，包括数字类型
- Dart 字符串是 UTF-16 代码单元(code unit)的序列，在字符串中表示32位 Unicode 值时需要特殊语法 `Runes`
- 在字符串前添加 r 来创建”原始”字符串，`var s = r'In a raw string`
- 在Dart中，数组是 `List` 对象
- Dart 支持集合字面量，`var halogens = {'fluorine', 'chlorine'}`
- `{}` 默认为 Map 类型，`var names = {};` 创建了 `Map` 而不是 `Set`
- Dart `new` 关键字是可选的 (Dart 2开始)
- Dart 中函数也是对象，其类型为 `Function`，可以将函数分配给变量或作为参数传递给其他函数
- Dart 中的箭头语法 `=> expr` 用于简化仅包含一个表达式的函数
- Dart 函数可以具有两种类型的参数： required和optional . 首先列出必需的参数，然后列出所有可选参数
- Dart 函数的可选参数可以是命名参数，也可以是位置参数
- Dart 函数可以使用 `=` 来定义命名参数和位置参数的默认值。默认值必须是编译时常量
- `~/` 返回除法的整数结果
- Dart 中的 `switch` 语句使用 `==` 比较整数、字符串、枚举或编译时常量
- Dart的所有异常都是未经检查的异常
- Dart 提供 `Exception` 和 `Error` 类型，并且支持将任何非 `null` 对象作为异常抛出
- 某些语言（例如Java）将文件的组织与类的组织联系在一起-每个文件只能定义一个顶级类. Dart没有此限制
- Dart 是一种具有类和基于 Mixin 的继承的面向对象语言
- Object 的 `runtimeType` 属性返回对象类型
- 所有实例变量都会生成一个隐式的 getter 方法. 非 final 的实例变量还会生成隐式的 setter 方法
- Dart 中每个类都隐式定义一个接口
- Dart 泛型在运行时会携带其类型信息 (相反，Java中 的泛型使用了 erasure ，这意味着在运行时会删除泛型类型参数. 在 Java 中，您可以测试对象是否为 List，但不能测试对象是否为 List )
- Dart 使用 async 和 await 关键字支持异步编程，使您可以编写看起来类似于同步代码的异步代码
- 可以使用 Future API 或 `async/await` 关键字处理 Future 结果
- 可以使用 Stream API 或 `await for` 处理 Stream 结果
- 仅 dart2js 支持延迟加载库. Flutter，Dart VM和dartdevc不支持延迟加载

```
// 延迟导入库
import 'package:greetings/hello.dart' deferred as hello;

// 使用库
Future greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

# 断言

开发过程中可以使用 `assert(condition , optionalMessage)` 断言，检查某些条件是否为真。断言通常由工具或框架决定是否生效：

- Flutter 在 debug 模式下启用断言
- 默认情况下，仅开发工具（例如 [dartdevc](http://s0dart0dev.icopy.site/tools/dartdevc)）启用断言
- 某些工具，比如 [dart](http://s0dart0dev.icopy.site/server/tools/dart-vm) 通过 `--enable-asserts` 标志启用断言

# 构造函数

Dart 中的构造函数跟 Java 中的构造函数还是有不小的区别，所以值得独立作为一节来讨论。

这里先列出了 Dart 构造函数相关的一些术语。

- Default constructors
- Named constructors
- Initializer list
- Redirecting constructors
- Constant constructors
- Factory constructors

如果你清楚这些术语，说明你已经基本掌握了 Dart 构造函数，完全可以略过本节。如果不清楚，不妨往下看。

Dart 中通过创建一个与其类具有相同名称的函数来声明一个构造函数。可以很方便地将构造函数参数赋值给实例变量：

```
class Point {
  num x, y;

  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

Dart 中使用命名构造函数可为一个类实现多个构造函数或提供额外的清晰度：

```
class Point {
  num x, y;

  Point(this.x, this.y);

  // Named constructor
  Point.origin() {
    x = 0;
    y = 0;
  }
}
```

构建函数的执行顺序如下：

- 初始化列表
- 超类的无参数构造函数
- 主类的无参数构造函数

注意：如果超类没有未命名，无参数的构造函数，则必须手动调用超类中的构造函数之一

```
class Employee extends Person {
  Employee() : super.fromJson(getDefaultData());
  // ···
}
```

在实现并非总是创建其类的新实例的构造函数时，要使用 `factory` 关键字。示例如下：

```
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(
        name, () => Logger._internal(name));
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

# 隐式接口

每个类都隐式定义一个接口。

```
// A person. The implicit interface contains greet().
class Person {
  // In the interface, but visible only in this library.
  final _name;

  // Not in the interface, since this is a constructor.
  Person(this._name);

  // In the interface.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// An implementation of the Person interface.
class Impostor implements Person {
  get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}
```

# Mixin

Mixins是在多个类层次结构中重用类代码的一种方式。

首先看如何实现 mixin。使用 `mixin` 关键字创建一个扩展自 Object 且不声明构造函数的类。还可以使用 `on` 关键字来限定可以使用该 mixin 的类

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

mixin MusicalPerformer on Musician {
  // ···
}
```

再来看如何使用 mixin

# 语法糖

从设计者角度来说是一些锦上添花的语言特性，但从开发者角度来确实很方便。

类型推断

```
num highScore(List<num> scores) {
  var highest = 0;
  for (var score in scores) {
    if (score > highest) highest = score;
  }
  return highest;
}
```

扩展操作符 `...`

```
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

nullable 扩展操作符 `...?`

```
var list;
var list2 = [0, ...?list];
assert(list2.length == 1);
```

命名参数 (Named parameters) `paramName : value`

```
// 定义命名参数
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold, bool hidden}) {...}

// 指定命名参数
enableFlags(bold: true, hidden: false);
```

位置参数 (Positional parameters)

```
// 使用[]标记一组可选的位置参数
String say(String from, String msg, [String device]) {
  ...
}
```

级联操作符 `..`。这个操作符可以节省创建临时变量的步骤。

```
void main() {
  querySelector('#sample_text_id')
    ..text = 'Click me!'
    ..onClick.listen(reverseText);
}
```

匿名函数

```
var list = ['apples', 'bananas', 'oranges'];
list.forEach((item) {
  print('${list.indexOf(item)}: $item');
});
```

`??=` 操作符。这个操作符让代码更简洁

```
// Assign value to b if b is null; otherwise, b stays the same
b ??= value;
```

`??` 操作符。这个操作符让代码更简洁

```
// 如果 name 为 null 则返回 'Guest'
String playerName(String name) => name ?? 'Guest';
```

`?.` 操作符，表示有条件的成员访问，最左边的操作数可以为 null

typedef 用于给函数类型提供一个名称

```
typedef Compare = int Function(Object a, Object b);

class SortedCollection {
  Compare compare;

  SortedCollection(this.compare);
}

// Initial, broken implementation.
int sort(Object a, Object b) => 0;

void main() {
  SortedCollection coll = SortedCollection(sort);
  assert(coll.compare is Function);
  assert(coll.compare is Compare);
}
```

# 其他内容

- 使用 `operator` 来重载操作符
- noSuchMethod
- callable class。对于实现了 `call()` 方法的类，可以像调用函数一样调用该类的实例

# 参考

https://renato.athaydes.com/posts/interesting-dart-features.html#quick-dart-overview