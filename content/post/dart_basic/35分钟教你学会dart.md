---
title: "35分钟教你学会dart"
date: 2021-09-09T11:05:12+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

在本教程中，您将了解 Dart 基础知识，例如：

- 变量、数据类型和运算符
- 条件和循环
- collection
- function

完成后，您就可以直接使用 Dart 进行 Flutter 开发了。

## 入门

单击页面顶部或底部的“*下载资料”*按钮下载示例代码。您不会在本教程中构建示例项目，但可以使用编码示例作为参考。

您可以将*main.dart 中*的代码粘贴到 DartPad 中，也可以使用*Dart SDK*运行该文件。

要快速入门，最好使用开源工具[*DartPad*](https://dartpad.dev/)，它可以让您通过 Web 浏览器编写和测试 Dart 代码：

[![DartPad 编辑器，注释以显示下方每个元素的位置](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dartpad-1-650x329.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dartpad-1.png)

DartPad 的设置类似于典型的 IDE。它包括以下组件：

- *编辑器窗格*：位于左侧。您的代码将放在此处。
- *RUN 按钮*：在编辑器中运行代码。
- *控制台*：位于右上角，显示输出。
- *文档面板*：位于右下角，显示有关代码的信息。
- *示例*：此下拉列表显示了一些示例代码。
- *Null Safety 按钮*：使用此*按钮*可选择加入 Dart 的新声音零安全功能。
- *版本信息*：在右下角，DartPad 显示当前使用的 Flutter 和 Dart 版本。

如果您愿意，可以在您的机器上本地安装 Dart SDK。一种方法是[安装 Flutter SDK](https://flutter.dev/docs/get-started/install)。安装 Flutter 也会安装 Dart SDK。

要直接安装 Dart SDK，请访问https://dart.dev/get-dart。

## 为什么是flutter？

Dart 与 Java、C#、Swift 和 Kotlin 等其他语言有许多相似之处。它的一些功能包括：

- 静态类型
- 类型推断
- 字符串表达式
- 多范式，包括面向对象和函数式编程
- 空安全

Dart 已针对在各种平台上开发快速应用程序进行了优化。

## 核心概念

Dart 程序以调用`main`. Dart 的语法`main`看起来类似于 C、Swift 或 Kotlin 等其他语言的语法。

清除默认 DartPad 中的所有代码并添加`main`到编辑器中：

```dart
void main() {
  
}
```

你会看到之前有一个返回类型`main`。在这种情况下，它是`void`，意味着`main`不会返回任何东西。

后面的括号`main`表示这是一个函数定义。大括号包含函数体。

在里面`main`，你为你的程序添加 Dart 代码。

接下来，您将了解有关以下核心概念的更多信息：

- 变量、注释和数据类型
- 基本dart类型
- Operators
- 字符串
- 不变性
- 可空性
- 条件和中断
- For 循环

是时候深入了解了

## 变量、注释和数据类型

您要添加的第一件事`main`是变量赋值语句。*变量*保存您的程序将处理的数据。

您可以将变量视为计算机内存中保存值的盒子。每个框都有一个名称，即变量的名称。要使用 Dart 表示变量，请使用`var`关键字。

添加一个新变量到`main`：

```dart
var myAge = 35;  


```

每个 Dart 语句都以分号结尾，就像 C 和 Java 中的语句一样。在上面的代码中，您创建了一个变量`myAge`，并将其设置为等于*35*。

您可以使用`print`Dart 中的内置将变量打印到控制台。在变量之后添加该调用：

```dart
print(myAge); // 35

```

在 DartPad 中单击*RUN*以运行代码。您将在控制台中看到变量的值*35*。

[![第一次飞镖输出](https://luckly007.oss-cn-beijing.aliyuncs.com/image/first_output-2-650x339.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/first_output-2.png)

### 注释

Dart 中的注释与 C 和其他语言中的注释类似：后面的文本`//`是单行注释，而其中的文本`/* ... */`是多行注释块。

下面是一个例子：

```dart
// This is a single-line comment.

print(myAge); // This is also a single-line comment.

/*
 This is a multi-line comment block. This is useful for long
 comments that span several lines.
 */


```

### 数据类型

Dart 是*静态类型的*，这意味着 Dart 中的每个变量都有一个在编译代码时必须知道的类型。运行程序时变量类型不能改变。C、Java、Swift 和 Kotlin 也是静态类型的。

这与*动态类型的*Python 和 JavaScript 等语言形成对比。这意味着在运行程序时变量可以保存不同类型的数据。编译代码时不需要知道类型。

单击`myAge`编辑器窗口并查看*文档*面板。你会看到 Dart*推断出*它`myAge`是一个，`int`因为它是用整数值*35*初始化的。

如果您没有明确指定数据类型，Dart 会使用*类型推断*来尝试确定它，就像 Swift 和 Kotlin 所做的那样。

[![类型推断](https://luckly007.oss-cn-beijing.aliyuncs.com/image/type_inference-650x339.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/type_inference.png)

Dart 还对`int`. 输入一个变量，`pi`，等于 3.14：

```dart
var pi = 3.14;

print(pi); // 3.14


```

Dart 推断`pi`为 a 是`double`因为您使用了一个浮点值来初始化它。您可以在 Dart 信息面板中通过单击 来验证`pi`。

[![双数据类型推断](https://luckly007.oss-cn-beijing.aliyuncs.com/image/pi_output-650x339.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/pi_output.png)

或者，您可以声明类型，而不是使用类型推断。

### 基本dart类型

Dart 使用以下基本类型：

- *int* : 整数
- *double* : 浮点数
- *bool* : 布尔值
- *字符串*：字符序列

以下是每种 Dart 类型的示例：

[![Dart 数据类型](https://luckly007.oss-cn-beijing.aliyuncs.com/image/datatypes-650x314.png)](https://koenig-media.raywenderlich.com/uploads/2020/08/datatypes.png)

`int`并且`double`都派生自一个名为`num`. `num`使用`dynamic`关键字来模拟静态类型 Dart 中的动态类型。

通过替换`var`为您要使用的类型来执行此操作：

```dart
int yourAge = 27;

print(yourAge); // 27


```

### 动态关键字

如果您使用`dynamic`关键字而不是`var`，您将获得有效的动态类型变量：

```dart
dynamic numberOfKittens;


```

在这里，您可以设置`numberOfKittens`为`String`使用引号。您将在本教程的后面了解有关`String`该类型的更多信息。

```dart
numberOfKittens = 'There are no kittens!';

print(numberOfKittens); // There are no kittens!


```

`numberOfKittens`有一个类型，因为 Dart 有静态类型。但该类型是`dynamic`，这意味着您可以为它分配具有其他类型的其他值。因此，您可以`int`在打印语句下方分配一个值。

```dart
numberOfKittens = 0;

print(numberOfKittens); // 0


```

或者，如果你在[薛定谔的盒子里](https://en.wikipedia.org/wiki/Schrödinger's_cat)有一只小猫，你可以赋值`double`：

```dart
numberOfKittens = 0.5;

print(numberOfKittens); // 0.5


```

[![薛定谔猫](https://luckly007.oss-cn-beijing.aliyuncs.com/image/schrodingers-cat-650x437.png)](https://koenig-media.raywenderlich.com/uploads/2020/08/schrodingers-cat.png)

单击*运行*以查看`numberOfKittens`控制台中打印的三个不同值。在每种情况下， 的类型`numberOfKittens`仍然存在`dynamic`，即使变量本身持有不同类型的值。

[![Dart 动态数据类型](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dynamic_data_type-650x239.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dynamic_data_type.png)

### 布尔值

该`bool`类型包含`true`或 的值`false`。

```dart
bool areThereKittens = false;

print(areThereKittens); // false


```

但是，如果你看看薛定谔的盒子，你可能会转而养一只活生生的小猫：

```dart
bool areThereKittens = false;

print(areThereKittens); // false


```

再次运行代码以在控制台中查看您的布尔值。你在盒子里看是一件好事！:]

[![Dart bool 数据类型](https://luckly007.oss-cn-beijing.aliyuncs.com/image/bool_data_type-650x276.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/bool_data_type.png)

## 操作符

Dart 具有您在其他语言（如 C、Swift 和 Kotlin）中熟悉的所有常用运算符。

Dart 运算符的一些示例包括：

- 算术
- 平等
- 递增和递减
- 比较
- 合乎逻辑的

*注意*：Dart 还允许*运算符重载*，就像在 C++ 和 Kotlin 中一样，但这超出了本教程的范围。要了解有关该主题的更多信息，请访问 Wikipedia 的[overator 重载](https://en.wikipedia.org/wiki/Operator_overloading)页面。

接下来，您将了解这些运算符中的每一个。

### 算术运算符

算术运算符的工作方式与您期望的一样。通过向 DartPad 添加一系列操作来尝试它们：

```dart
print(40 + 2); // 42

print(44 - 2); // 42

print(21 * 2); // 42

print(84 / 2); // 42.0


```

对于除法，即使是整数，Dart 也会将结果变量推断为 a `double`。这就是为什么最后一条语句得到*42.0*而不是 42的原因`print`。

*注意*：DartPad 在控制台中将“84 / 2”的结果显示为 42，因为它将输出到控制台的格式设置为仅显示有效数字。如果您在 Dart SDK 的 Dart 程序中打印相同的语句，您将得到*42.0*作为结果。

### 等号运算符

Dart 使用*双等于*( `==`) 等于和*不等于*( `!=`) 运算符：

```dart
print(42 == 43); // false

print(42 != 43); // true


```

### 比较运算符

Dart 使用典型的比较运算符：

- 小于 (<)
- 大于 (>)
- 等于 (=>)

这里有些例子：

```dart
print(42 < 43); // true print(42 >= 43); // false


```

此外，它还使用常用的复合算术/赋值运算符：

```dart
var value = 42.0;

value += 1; print(value); // 43.0

value -= 1; print(value); // 42.0

value *= 2; print(value); // 84.0

value /= 2; print(value); // 42.0


```

复合算术/赋值运算符执行两项任务。`+=`将右边的值与左边的变量相加，然后将结果赋给变量。

的缩写形式`+= 1`是`++`：

```dart
value++;

print(value); // 43.0


```

Dart 有通常的模运算符 ( `%`) 来返回一个数除以另一个数后的余数：

```dart
print(392 % 50); // 42


```

392 ÷ 50 = 7.84，但结果窗格中的 42 来自哪里？用长除法更容易看到。

[![长除法显示 392 除以 50，余数为 42](https://luckly007.oss-cn-beijing.aliyuncs.com/image/long_division_392_50.png)](https://koenig-media.raywenderlich.com/uploads/2021/06/long_division_392_50.png)

### 逻辑运算符

DART使用相同的逻辑运算符为其他语言，包括`&&`了*和*和`||`的*OR*。

```dart
print((41 < 42) && (42 < 43)); // true

print((41 < 42) || (42 > 43)); // true


```

否定运算符是*感叹号*，它把假变成真，真变成假。

```dart
print(!(41 < 42)); // false


```

有关[dart运算符](https://dart.dev/guides/language/language-tour#operators)的完整列表，请参阅 Dart 文档。

## 字符串

Dart 字符串类型是`String`. 字符串在 Dart 中使用由*单**引号*或*双引号括起来的*文本表示。

您可以使用`var`and 类型推断或`String`创建字符串变量，就像您见过的其他类型一样：

```dart
var firstName = 'Albert';

String lastName = "Einstein";


```

与 Kotlin 和 Swift 等语言类似，您可以使用美元符号将表达式的值嵌入到字符串中：*${ \*expression\* }*。

如果表达式是标识符，则可以省略*{ }*。添加以下内容：

```dart
var physicist = "$firstName $lastName likes the number ${84 / 2}";

print(physicist); // Albert Einstein


```

`$firstName`和`$lastName`由可变值替换。在``返回计算的结果。

### 转义字符串

Dart 中使用的转义序列类似于其他类 C 语言中使用的转义序列。例如，您用于`\n`换行。

如果字符串中有特殊字符，请使用`\`转义它们：

```dart
var quote = 'If you can\'t explain it simply\nyou don\'t understand it well enough.';

print(quote);

// If you can't explain it simply

// you don't understand it well enough.


```

此示例使用单引号，因此它需要一个转义序列 ,`\'`将*can't*和*don't*的撇号嵌入到字符串中。如果您改用双引号，则不需要转义撇号。

如果您需要在字符串中显示转义序列，您可以使用以 为前缀的*原始字符串*`r`。

```dart
var rawString = r"If you can't explain it simply\nyou don't understand it well enough.";

print(rawString); 

// If you can't explain it simply\nyou don't understand it well enough.


```

在这里，Dart 将其``\n``视为普通文本，因为字符串以`r`.

单击DartPad 中的*RUN*以在控制台中查看所有字符串。

[![Dart 字符串数据类型](https://luckly007.oss-cn-beijing.aliyuncs.com/image/String_data_type-650x276.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/String_data_type.png)

## 不变性

Dart 使用关键字`const`和`final`不改变的值。

使用`const`对于那些在编译时已知值。使用`final`了不具备在编译时已知的，但初始化后不能被重新分配的值。

*注意*：`final`就像`val`在 Kotlin 或`let`Swift 中一样。

您可以使用`const`and`final`代替`var`并让类型推断确定类型：

```dart
const speedOfLight = 299792458;

print(speedOfLight); // 299792458


```

在这里，Dart 推断这`speedOfLight`是一个`int`，正如您在 DartPad 的信息面板中看到的那样。

[![Dart 常量推断](https://luckly007.oss-cn-beijing.aliyuncs.com/image/const_inference-650x239.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/const_inference.png)

`final`表示变量是*不可变的*，这意味着您不能重新分配`final`值。您可以使用`final`或显式声明类型`const`：

```dart
final planet = 'Jupiter';

// planet = 'Mars';

// error: planet can only be set once

final ​String moon = 'Europa';

print('$planet has a moon, $moon');

// Jupiter has a moon, Europa
```

## 可空性

过去，如果你没有初始化一个变量，Dart 会给它赋值`null`，这意味着变量中*没有*存储*任何内容*。不过，从 Dart 2.12 开始，Dart 加入了其他语言，如 Swift 和 Kotlin，默认情况下不可为空。

此外，Dart*保证*不可为空的类型永远不会包含空值。这被称为[*空安全*](https://dart.dev/null-safety)。

通常，如果要声明一个变量，必须对其进行初始化：

```dart
String middleName = 'May';

print(middleName); // May


```

但是，并非每个人都有中间名，因此创建`middleName`一个可为空的值是有意义的。要告诉 Dart 您想要允许 value `null`，请`?`在类型后添加。

```dart
String? middleName = null;

print(middleName); // null


```

可空类型的默认值是`null`，因此您可以将表达式简化为以下内容：

```dart
String? middleName;

print(middleName); // null


```

运行它并`null`打印到控制台。

[![Dart 空字符串输出](https://luckly007.oss-cn-beijing.aliyuncs.com/image/null_string_output-650x239.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/null_string_output.png)

### 空感知运算符

Dart 有一些可*识别空值的*运算符，您可以在处理空值时使用。

双问号运算符 ，`??`就像Kotlin 中的*Elvis 运算符*：如果对象不为空，它返回左侧操作数。否则，它返回右边的值：

```dart
var name = middleName ?? 'none';

print(name); // none


```

由于`middleName`is `null`，Dart 分配 的右侧值`'none'`。

该`?.运算符保护您访问空对象的属性。`null`如果对象本身为空，则返回。否则，它返回右侧属性的值：

```dart
print(middleName?.length); // null

```

在空安全之前的日子里，如果你忘记了问号并写了`middleName.length`，如果`middleName`为空，你的应用程序会在运行时崩溃。这不再是问题，因为 Dart 现在会立即告诉您何时需要处理空值。

## 控制流

*控制流*可让您决定何时执行、跳过或重复某些代码行。您可以使用*条件*和*循环*来处理 Dart 中的控制流。

在本节中，您将了解更多关于：

- 条件句
- While 循环
- 继续和中断
- For 循环

以下是您需要了解的有关 Dart 中的控制流元素的信息。

## 条件句

控制流的最基本形式是根据程序运行时发生的条件决定是执行还是跳过代码的某些部分。

处理条件的语言结构是`if`/`else`语句。`if`/`else`在 Dart 中看起来几乎与它在其他类 C 语言中的使用相同。

### 如果语句

假设您有一个变量，`animal`，它当前是一只狐狸。它看起来像这样：

```dart
var animal = 'fox';

```

[![狐狸](https://luckly007.oss-cn-beijing.aliyuncs.com/image/creature-head-fox-500x500.png)](https://koenig-media.raywenderlich.com/uploads/2020/08/creature-head-fox.png)

您可以使用`if`语句来检查`animal`是猫还是狗，然后运行一些相应的代码。

```dart
if (animal == 'cat' || animal == 'dog') {
  print('Animal is a house pet.');
}

```

在这里，您使用了*相等*和*OR*运算符`bool`为`if`语句创建了一个内部条件。

### 其他语句

使用`else`子句，如果条件为假，您可以运行替代代码：

```dart
else {
  print('Animal is NOT a house pet.');
}
// Animal is NOT a house pet.

```

您也可以将多个组合`if`/`else`语句成`if`/ `else if`/`else`结构：

```dart
if (animal == 'cat' || animal == 'dog') {
  print('Animal is a house pet.');
} else if (animal == 'rhino') {
  print('That\'s a big animal.');
} else {
  print('Animal is NOT a house pet.');
}
// Animal is NOT a house pet.

```

你可以有很多`else if`分支之间`if`，并`else`根据您的需要。

## While 循环

循环允许您重复代码一定次数或基于特定条件。您可以使用*while 循环*处理基于条件的重复。

Dart 中有两种形式的 while 循环：`while`和`do-while`。不同之处在于 for `while`，循环条件发生在代码块之前。在`do-while`，条件发生在之后。这意味着`do-while`循环确保代码块至少运行一次。

### 测试 While 循环

要尝试此操作，请创建一个`i`初始化为 1的变量：

```dart
var i = 1;

```

接下来，使用`while`循环`i`在递增时打印。设置条件`i`为小于10：

```dart
while (i < 10) {
  print(i);
  i++;
}
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9

```

运行代码，您将看到`while`循环打印数字 1 到 9。

### 尝试 Do-While 循环

`i`在 DartPad 中重置，然后添加一个`do-while`循环：

```dart
i = 1;
do {
  print(i);
  i++;
} while (i < 10);
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9

```

结果和以前一样。然而这一次，循环体在检查循环退出条件之前运行了一次。

## 继续和中断

Dart在循环和其他地方使用`continue`和`break`关键字。以下是他们所做的：

- *continue*：跳过循环中剩余的代码并立即进入下一次迭代。
- *break*：停止循环并在循环体之后继续执行。

`continue`在代码中使用时要小心。例如，如果您`do-while`从上面进行循环，并且您希望在`i`等于 5时继续，则可能会导致*无限循环，*具体取决于您放置`continue`语句的位置：

```dart
i = 1;
do {
  print(i);
  if (i == 5) {
    continue;
  }            
  ++i;
} while (i < 10);
// 1
// 2
// 3
// 4
// 5
// 5
// 5
// 5
// 5
// 5
// 5
// 5
// 5
// 5
// ...

```

发生无限循环的原因是，一旦`i`为 5，您就不会再增加它，因此条件始终为真。

如果在 DartPad 中运行它，无限循环将导致浏览器挂起。相反，使用`break`，因此循环在`i`达到 5后结束：

```dart
i = 1;
do {
  print(i);
  if (i == 5) {
    break;
  }
  ++i;
} while (i < 10);
// 1
// 2
// 3
// 4
// 5

```

运行代码。现在，循环在五次迭代后结束。

## For 循环

在 Dart 中，您使用`for`循环来循环预定次数。`for`循环由初始化、循环条件和动作组成。再一次，它们类似于`for`其他语言中的循环。

Dart 还提供了一个`for-in`循环，它遍历一组对象。稍后您将了解有关这些的更多信息。

要查看`for`循环的工作原理，请为总和创建一个变量：

```dart
var sum = 0;

```

接下来，使用`for`循环将循环计数器从`i`1初始化。然后您将检查它`i`是否小于或等于 10，并`i`在每次循环后递增。

在循环内，使用复合赋值添加`i`到运行总和：

```dart
for (var i = 1; i <= 10; i++) {
  sum += i;  
}
print("The sum is $sum"); // The sum is 55

```

在 DartPad 中运行您的代码以查看总和。

[![Dart for 循环](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dart_for_loop-650x221.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dart_for_loop.png)

# Collections

集合对于对相关数据进行分组很有用。Dart 包括几种不同类型的集合，但本教程将介绍两种最常见的：`List`和`Map`.

## 列表

Dart 中的*列表*类似于其他语言中的*数组*。您可以使用它们来维护有序的值列表。列表是从零开始的，因此列表中的第一项位于索引 0 处：

[![甜点清单](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dart_list-650x166.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dart_list.png)

以下是不同甜点的清单：

```dart
List desserts = ['cookies', 'cupcakes', 'donuts', 'pie'];

```

您将列表的元素括在方括号中：`[ ]`。使用逗号分隔元素。

在该行的开头，您可以看到类型为`List`。您会注意到没有包含类型。Dart 推断该列表具有类型。`List`

这是一个整数列表：

```dart
final numbers = [42, -1, 299792458, 100];

```

单击`numbers`DartPad，您将看到 Dart 将类型识别为 a `List`of `int`。

[![int的飞镖列表](https://luckly007.oss-cn-beijing.aliyuncs.com/image/list_of_integers-650x221.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/list_of_integers.png)

### 使用列表元素

要访问列表的元素，请使用*下标表示法*，将索引号放在列表变量名称之后的方括号之间。例如：

```dart
final firstDessert = desserts[0];
print(firstDessert); // cookies

```

由于列表索引是从零开始的，因此`desserts[0]`是列表的第一个元素。

分别使用`add`和 来添加和删除元素`remove`：

```dart
desserts.add('cake');
print(desserts); 
// [cookies, cupcakes, donuts, pie, cake]

desserts.remove('donuts');
print(desserts); 
// [cookies, cupcakes, pie, cake]

```

运行代码查看结果。

[![Dart 列表添加和删除方法](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dart_list_methods-650x250.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dart_list_methods.png)

早些时候，您了解了`for`循环。Dart 的`for-in`循环尤其适用于列表。试试看：

```dart
for (final dessert in desserts) {
  print('I love to eat $dessert.');
}
// I love to eat cookies.
// I love to eat cupcakes.
// I love to eat pie.
// I love to eat cake.

```

您不需要使用索引。Dart 只是遍历 的每个元素`desserts`并将其每次分配给名为 的变量`dessert`。

饿了？好吧，在你吃完蔬菜之前，你不能吃任何甜点。:]

## map

当你想要一个配对值列表时，`Map`是一个不错的选择。Dart`Map`类似于Swift 中的*字典*和Kotlin 中的*映射*。

[![飞镖地图](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dart_maps-650x347.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dart_maps.png)

以下是 Dart 中的地图示例：

```dart
Map<String, int> calories = {
  'cake': 500,
  'donuts': 150,
  'cookies': 100,
};

```

您围绕`Map`s的花括号`{ }`。使用逗号分隔地图的元素。

映射的元素称为*键值对*，其中键位于冒号左侧，值位于右侧。

您可以通过使用键查找值来查找值，如下所示：

```dart
final donutCalories = calories['donuts'];
print(donutCalories); // 150

```

键 ,`'donuts'`位于地图名称后面的方括号内。在这种情况下，它映射到 的值`150`。

单击`donutCalories`DartPad，您将看到推断的类型是`int?`而不是`int`。这是因为，如果地图不包含您要查找的键，它将返回一个`null`值。

[![飞镖图输出](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dart_map_output-650x230.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dart_map_output.png)

通过指定键并为其分配值来向映射添加新元素：

```dart
calories['brownieFudgeSundae'] = 1346;
print(calories);
// {cake: 500, donuts: 150, cookies: 100, brownieFudgeSundae: 1346}

```

运行该代码，您将看到以水平格式打印的地图，最后是您的新甜点。

# Functions

函数使您可以将多行相关代码打包到一个主体中。然后调用该函数以避免在整个 Dart 应用程序中重复这些代码行。

[![飞镖功能](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dart_function-650x259.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dart_function.png)

一个函数由以下元素组成：

- 返回类型
- 函数名
- 括号中的参数列表
- 括在括号中的函数体

### 定义函数

您要转换为函数的代码位于大括号内。调用函数时，传入与函数*参数*类型匹配的参数。

接下来，您将在 DartPad 中编写一个新函数，该函数将检查给定的字符串是否为*香蕉*：

```dart
bool isBanana(String fruit) {
  return fruit == 'banana';
}

```

该函数用于`return`返回一个`bool`. 您传递给函数的参数决定了`bool`.

对于任何给定的输入，此函数将始终返回相同的值类型。如果函数不需要返回值，您可以将返回类型设置为`void`. `main`例如，这样做。

### 使用函数

您可以通过传入字符串来调用该函数。然后，您可以将该调用的结果传递给`print`：

```dart
void main() {
  var fruit = 'apple';
  print(isBanana(fruit)); // false
}

```

### 嵌套函数

通常，您可以在其他函数外部或 Dart 类内部定义函数。但是，您也可以嵌套 Dart 函数。例如，您可以嵌套`isBanana`在`main`.

```dart
void main() {
  bool isBanana(String fruit) {
    return fruit == 'banana';
  }

  var fruit = 'apple';
  print(isBanana(fruit)); // false
}

```

您还可以将参数更改为函数，然后使用新参数再次调用它：

```dart
fruit = 'banana';
print(isBanana(fruit));  // true

```

调用函数的结果完全取决于你传入的参数。

### 可选参数

如果函数的参数是可选的，您可以用方括号将其括起来并使类型可以为空：

```dart
String fullName( String first, String last, [ String? title]) {
   if (title == null ) {
     return  ' $first  $last ' ; 
  } else {
     return  ' $title  $first  $last ' ; 
  } 
}
```

在这个函数中，`title`是可选的。`null`如果您不指定它，它将默认为。

现在，您可以使用或不使用可选参数调用该函数：

```dart
print(fullName('Joe', 'Howard'));
// Joe Howard

print(fullName('Albert', 'Einstein', 'Professor'));
// Professor Albert Einstein

```

### 命名参数和默认值

当您有多个参数时，很难记住哪个是哪个。飞镖解决了这一难题*命名参数*，您可以通过与周围大括号参数列表获得：`{ }`。

默认情况下，这些参数是可选的，但您可以为它们提供默认值或使用`required`关键字使它们成为必需：

```dart
bool withinTolerance({required int value, int min = 0, int max = 10}) {
  return min <= value && value <= max;
}

```

`value`是必需的，而`min`和`max`是可选的，具有默认值。

使用命名参数，您可以通过提供带有冒号的参数名称以不同的顺序传递参数：

```dart
print(withinTolerance(min: 1, max: 5, value: 11)); // false

```

调用函数时，您可以不使用默认值的参数。

```dart
print(withinTolerance(value: 5)); // true

```

运行您的代码以查看正在运行的新函数。

[![命名和默认参数](https://luckly007.oss-cn-beijing.aliyuncs.com/image/named_default_parameters-650x184.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/named_default_parameters.png)

### 匿名函数

Dart 支持*一流的 functions*，这意味着它像对待任何其他数据类型一样对待函数。您可以将它们分配给变量，将它们作为参数传递并从其他函数中返回它们。

要将这些函数作为值传递，请省略函数名称和返回类型。由于没有名称，这种类型的函数称为*匿名函数*。

[![匿名函数](https://luckly007.oss-cn-beijing.aliyuncs.com/image/dart_anonymous_function-650x159.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/dart_anonymous_function.png)

您可以将匿名函数分配给名为 的变量`onPressed`，如下所示：

```dart
final onPressed = () {
  print('button pressed');
};

```

`onPressed`有一个类型的值`Function`。空括号表示该函数没有参数。与常规函数一样，大括号内的代码是函数体。

要在函数体内执行代码，请调用变量名，就像它是函数名一样：

```dart
oonPressed(); // button pressed 按钮按下
```

您可以使用*箭头语法*简化函数体仅包含一行的函数。为此，请移除大括号并添加一个粗箭头`=>`。

这是上面匿名函数和重构版本的比较：

```dart
// original anonymous function
final onPressed = () {
  print('button pressed');
};

// refactored
final onPressed = () => print('button pressed');

```

读起来好多了。

### 使用匿名函数

你会经常看到 Flutter 中的匿名函数，就像上面的那些，作为 UI 事件的回调传递。这使您可以指定在用户执行某些操作（例如按下按钮）时运行的代码。

您会看到匿名函数的另一个常见地方是集合。您可以为集合提供一个匿名函数，该函数将对集合的每个元素执行某些任务。例如：

```dart
// 1
final drinks = ['water', 'juice', 'milk'];
// 2
final bigDrinks = drinks.map(
  // 3
  (drink) => drink.toUpperCase()
);
// 4
print(bigDrinks); // (WATER, JUICE, MILK)

```

让我们看看每一步：

1. 定义一个`drinks`包含小写字母的列表。
2. `.map` 获取所有列表值并返回一个带有它们的新集合。
3. 匿名函数作为参数传递。在该匿名函数中，您有一个`drink`表示列表中每个元素的参数。
4. 匿名函数的主体将每个元素转换为大写并返回值。由于原始列表是字符串列表，因此`drink`也有 type `String`。

使用匿名函数并将其与它结合`.map`是将一个集合转换为另一个集合的便捷方式。

*注意*：不要将`.map`方法与`Map`类型混淆。

运行代码以查看生成的集合。

[![匿名函数](https://luckly007.oss-cn-beijing.aliyuncs.com/image/anonymous_functions-1-650x248.png)](https://koenig-media.raywenderlich.com/uploads/2021/05/anonymous_functions-1.png)

恭喜，您已完成本教程。您现在应该对学习如何构建 Flutter 应用程序时看到的 Dart 代码有了更好的理解！

## 然后去哪儿？



另外，请查看[官方 Dart 文档](https://dart.dev/guides)。

我们希望您喜欢这篇关于 Dart 基础知识的简短介绍。如果您有任何问题或意见，请关注我。





flutter

电子书  https://www.syncfusion.com/succinctly-free-ebooks/flutter-succinctly



我计划出40集左右，给大家展示flutter基础使用

https://kodestat.gitbook.io/flutter/



https://hackr.io/blog/how-to-learn-flutter
