---
title: "Dart—_tips"
date: 2021-08-21T21:17:55+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

https://codewithandrea.com/videos/top-dart-tips-and-tricks-for-flutter-devs/

特意给大家带来我在开发中总结的dart相关的技巧

## 1. 你知道吗？Dart 支持字符串乘法。

这是一个简单的程序，显示如何使用字符串乘法打印圣诞树：

```
void main() {
  for (var i = 1; i <= 5; i++) {
    print('🎄' * i);
  }
}
// Output:
// 🎄
// 🎄🎄
// 🎄🎄🎄
// 🎄🎄🎄🎄
// 🎄🎄🎄🎄🎄
```

是不是很酷？😉

您可以使用它来检查长字符串如何适合`Text`小部件：

```
Text('You have pushed the button this many times:' * 5)
```

## 2.需要同时执行多个Future吗？使用 Future.wait。

考虑这个模拟 API 类，它告诉我们最新的 COVID 病例数：

```
// Mock API class
class CovidAPI {
  Future<int> getCases() => Future.value(1000);
  Future<int> getRecovered() => Future.value(100);
  Future<int> getDeaths() => Future.value(10);
}
```

要同时执行所有这些futures，请使用`Future.wait`. 这需要一个**列表或 **futures** and returns a **future of lists**:

```
final api = CovidAPI();
final values = await Future.wait([
    api.getCases(),
    api.getRecovered(),
    api.getDeaths(),
]);
print(values); // [1000, 100, 10]
```

This is ideal when the futures are **independent**, and they don't need to execute **sequentially**.

## 3. 在 Dart 类中实现“调用”方法，使它们像函数一样可调用。

这是一个示例`PasswordValidator`类：

```
class PasswordValidator {
  bool call(String password) {
    return password.length > 10;
  }
}
```

因为该方法名为`call`，我们可以声明一个类实例并将其**用作**方法：

```
final validator = PasswordValidator();
// can use it like this:
validator('test');
validator('test1234');
// no need to use it like this:
validator.call('not-so-frozen-arctic');
```

## 4. 需要调用回调但前提是它不为空？使用“?.call()”语法。

假设我们有一个自定义小部件类，它应该`onDragCompleted`在发生特定事件时调用回调：

```
class CustomDraggable extends StatelessWidget {
  const CustomDraggable({Key key, this.onDragCompleted}) : super(key: key);
  final VoidCallback? onDragCompleted;

  void _dragComplete() {
    // TODO: Implement me
  }
  @override
  Widget build(BuildContext context) {/*...*/}
}
```

要调用回调，我们可以编写以下代码：

```
  void _dragComplete() {
    if (onDragCompleted != null) {
      onDragCompleted();
    }
  }
```

但是有一个更简单的方法（注意使用`?.`）：

```
  Future<void> _dragComplete() async {
    onDragCompleted?.call();
  }
```

## 5. 使用匿名函数和函数作为参数

在 Dart 中，函数是**一等公民**，可以**作为参数**传递给其他函数。

下面是一些定义匿名函数并将其分配给`sayHi`变量的代码：

```
void main() {
  final sayHi = (name) => 'Hi, $name';
  welcome(sayHi, 'Andrea');
}

void welcome(String Function(String) greet,
             String name) {
  print(greet(name));
  print('Welcome to this course');
}
```

然后`sayHi`传递给一个`welcome`函数，该函数接受一个`Function`参数并使用它来迎接用户。

`String Function(String)`是一个**函数类型**，它接受一个`String`参数并返回一个`String`. 因为上面的匿名函数具有相同的**签名**，它可以直接作为参数传递，也可以通过变量传递`sayHi`。

------

使用功能等运营商时，这种编码风格是常见的`map`，`where`和`reduce`。

例如，这是一个计算数字平方的简单函数：

```
int square(int value) {
  // just a simple example
  // could be a complex function with a lot of code
  return value * value;
}
```

给定一个值列表，我们可以映射它们以获得平方：

```
const values = [1, 2, 3];

values.map(square).toList();
```

这里我们`square`作为参数传递，因为它的签名正是 map 操作符所期望的。这意味着我们不需要用匿名函数扩展它：

```
values.map((value) => square(value)).toList();
```

## 6. 您可以使用 collection-if 和 spreads 与lists, sets AND maps

当您将 UI 作为代码编写时，Collection-if 和 spreads 非常有用。

但是您知道您也可以将它们与maps一起使用吗？

考虑这个例子：

```
const addRatings = true;
const restaurant = {
  'name' : 'Pizza Mario',
  'cuisine': 'Italian',
  if (addRatings) ...{
    'avgRating': 4.3,
    'numRatings': 5,
  }
};
```

这里我们声明一个`restaurant`maps，只添加`avgRating`和`numRatings`键值对，如果`addRatings`是`true`。因为我们要添加多个键值对，所以我们需要使用扩展运算符 ( `...`)。

## 7. 需要以空安全的方式遍历map吗？使用`.entries`：

假设你有map：

```
const timeSpent = <String, double>{
  'Blogging': 10.5,
  'YouTube': 30.5,
  'Courses': 75.2,
};
```

以下是如何编写循环以使用所有键值对运行一些代码：

```
for (var entry in timeSpent.entries) {
  // do something with keys and values
  print('${entry.key}: ${entry.value}');
}
```

通过迭代`entries`变量，您可以以**空安全的方式**访问所有键值对。

这比这更简洁，更不容易出错：

```
for (var key in timeSpent.keys) {
  final value = timeSpent[key]!;
  print('$key: $value');
}
```

上面的代码`!`在读取值时需要使用断言运算符 ( )，因为 Dart 不能保证给定键的值存在。

## 8. 使用命名构造函数和初始化列表以获得更符合人体工程学的 API。

假设您要声明一个表示温度值的类。

你可以让你的类API明确支持**两个**摄氏和华氏两种命名的构造函数：

```
class Temperature {
  Temperature.celsius(this.celsius);
  Temperature.fahrenheit(double fahrenheit)
    : celsius = (fahrenheit - 32) / 1.8;
  double celsius;
}
```

这个类只需要一个**存储**变量来表示温度，并使用初始化列表将华氏温度转换为摄氏温度。

这意味着您可以像这样声明温度值：

```
final temp1 = Temperature.celsius(30);
final temp2 = Temperature.fahrenheit(90);
```

## 9. getter 和 setter

在`Temperature`上面的类中，`celsius`被声明为存储变量。

但是用户可能更喜欢以华氏度**获取**或**设置**温度。

这可以使用 getter 和 setter 轻松完成，它们允许您定义计算变量。这是更新的课程：

```
class Temperature {
  Temperature.celsius(this.celsius);
  Temperature.fahrenheit(double fahrenheit)
    : celsius = (fahrenheit - 32) / 1.8;
  double celsius;
  double get fahrenheit
    => celsius * 1.8 + 32;
  set fahrenheit(double fahrenheit)
    => celsius = (fahrenheit - 32) / 1.8;
}
```

这使得使用华氏度或摄氏度轻松获取或设置温度：

```
final temp1 = Temperature.celsius(30);
print(temp1.fahrenheit);
final temp2 = Temperature.fahrenheit(90);
temp2.celsius = 28;
```

**底线**：使用命名构造函数、getter 和 setter 来改进类的设计。

## 10. 对未使用的函数参数使用下划线

在 Flutter 中，我们经常使用带有函数参数的小部件。一个常见的例子是`ListView.builder`：

```
class MyListView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemBuilder: (context, index) => ListTile(
        title: Text('all the same'),
      ),
      itemCount: 10,
    );
  }
}
```

在这种情况下，我们不使用`(context, index)`的参数`itemBuilder`。所以我们可以用下划线代替它们：

```
ListView.builder(
  itemBuilder: (_, __) => ListTile(
    title: Text('all the same'),
  ),
  itemCount: 10,
)
```

*注意：这两个参数是不同的 (`_`和`__`)，因为它们是**单独的标识符**。*

## 11. 需要一个只能实例化一次的类（又名单例）？使用带有私有构造函数的静态实例变量。

单例最重要的特性是整个程序中只能有**一个**它的**实例**。这对于建模文件系统之类的东西很有用。

```
// file_system.dart
class FileSystem {
  FileSystem._();
  static final instance = FileSystem._();
}
```

要在 Dart 中创建单例，您可以声明一个命名构造函数并使用`_`语法将其设为私有。

然后，您可以使用它来创建类的一个静态最终实例。

因此，其他文件中的任何代码都只能通过`instance`变量访问此类：

```
// some_other_file.dart
final fs = FileSystem.instance;
// do something with fs
```

*注意：如果您不小心，final可能会导致许多问题。在使用它们之前，请确保您了解它们的缺点。*

## 12. 需要收集独特的set？使用集合而不是列表。

Dart 中最常用的集合类型是`List`.

但是列表可以有重复的项目，有时这不是我们想要的：

```
const citiesList = [
  'London',
  'Paris',
  'Rome',
  'London',
];
```

我们可以`Set`在需要一组唯一值时使用 a （请注意 的使用`final`）：

```
// set is final, compiles
final citiesSet = {
  'London',
  'Paris',
  'Rome',
  'London', // Two elements in a set literal shouldn't be equal
};
```

上面的代码生成一个警告，因为`London`被包含了两次。如果我们尝试对`const`set执行相同的操作，则会收到错误并且我们的代码无法编译：

```
// set is const, doesn't compile
const citiesSet = {
  'London',
  'Paris',
  'Rome',
  'London', // Two elements in a constant set literal can't be equal
};
```

当我们与台合作，我们能够获得有用的API，如`union`，`difference`和`intersection`：

```
citiesSet.union({'Delhi', 'Moscow'});
citiesSet.difference({'London', 'Madrid'});
citiesSet.intersection({'London', 'Berlin'});
```

> 底线：当你创建一个集合时，问问自己你是否希望它的项目是独一无二的，并考虑使用一个集合。

## 13.如何使用try、on、catch、rethrow、finally

`try`并且`catch`在使用基于 Future 的 API 时非常理想，如果出现问题，这些 API 可能会引发异常。

这是一个完整的示例，展示了如何充分利用它们：

```
Future<void> printWeather() async {
  try {
    final api = WeatherApiClient();
    final weather = await api.getWeather('London');
    print(weather);
  } on SocketException catch (_) {
    print('Could not fetch data. Check your connection.');
  } on WeatherApiException catch (e) {
    print(e.message);
  } catch (e, st) {
    print('Error: $e\nStack trace: $st');
    rethrow;
  } finally {
    print('Done');
  }
}
```

一些注意事项：

- 您可以添加多个`on`子句来处理不同类型的异常。
- 您可以使用回退`catch`子句来处理与上述任何类型都不匹配的所有异常。
- 您可以使用`rethrow`语句将当前异常向上抛出调用堆栈，**同时保留堆栈跟踪**。
- 您可以使用`finally`在`Future`完成后运行一些代码，无论它是成功还是失败。

如果您正在使用或设计一些基于 Future 的 API，请确保根据需要处理异常。

## 14. 常见的 Future 构造函数

Dart`Future`类带有一些方便的工厂构造函数：`Future.delayed`,`Future.value`和`Future.error`。

我们可以`Future.delayed`用来创建一个`Future`等待一定延迟的。第二个参数是一个（可选的）匿名函数，你可以用它来完成一个值或抛出一个错误：

```
await Future.delayed(Duration(seconds: 2), () => 'Latte');
```

但有时我们想创建一个`Future`立即完成的：

```
await Future.value('Cappuccino');
await Future.error(Exception('Out of milk'));
```

我们可以用`Future.value`一个值来成功完成，或者`Future.error`用一个错误来完成。

您可以使用这些构造函数来模拟来自基于 Future 的 API 的响应。这在您的测试代码中编写模拟类时很有用。

## 15. 通用流构造器

Stream 类还带有一些方便的构造函数。以下是最常见的：

```
Stream.fromIterable([1, 2, 3]);
Stream.value(10);
Stream.empty();
Stream.error(Exception('something went wrong'));
Stream.fromFuture(Future.delayed(Duration(seconds: 1), () => 42));
Stream.periodic(Duration(seconds: 1), (index) => index);
```

- 用于从值列表`Stream.fromIterable`创建一个`Stream`。
- 使用`Stream.value`，如果你只有一个值。
- 用于`Stream.empty`创建空流。
- 用于`Stream.error`创建包含错误值的流。
- 用于`Stream.fromFuture`创建仅包含一个值的流，该值将在未来完成时可用。
- 用于`Stream.periodic`创建周期性的事件流。您可以将 a 指定`Duration`为事件之间的时间间隔，并指定一个匿名函数来生成给定其在流中的索引的每个值。

## 16. 同步和异步生成器

在 Dart 中，我们可以将**同步**生成器定义为一个返回 的函数`Iterable`：

```
Iterable<int> count(int n) sync* {
  for (var i = 1; i <= n; i++) {
    yield i;
  }
}
```

这使用`sync*`语法。在函数内部，我们可以“生成”或`yield`多个值。这些将`Iterable`在函数完成时返回。

------

另一方面，**异步**生成器是一个返回 a 的函数`Stream`：

```
Stream<int> countStream(int n) async* {
  for (var i = 1; i <= n; i++) {
    yield i;
  }
}
```

这使用此`async*`语法。在函数内部，我们可以`yield`像在同步情况下一样取值。

但是如果我们愿意，我们可以使用`await`基于 Future 的 API，因为这是一个**异步**生成器：

```
Stream<int> countStream(int n) async* {
  for (var i = 1; i <= n; i++) {
    // dummy delay - this could be a network request
    await Future.delayed(Duration(seconds: 1));
    yield i;
  }
}
```

