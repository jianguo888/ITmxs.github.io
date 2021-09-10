---
title: "编程中ifelse的作用是什么？我们为什么要在乎它"
date: 2021-08-15T11:27:55+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

# 编程中的 if else 语句是什么？我如何使用它们？我为什么要在乎？

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/IMG_6959.jpg)

我们今天要看看的是“if”和“else”语句。我相信这些是编程中更容易掌握的概念之一，因为它们与我们的自然语言非常相似。我会尽我最大的努力使它们成为一个易于理解的概念。

#### 什么是 if else 语句？

所以首先让我们从解释它们是什么开始。if else 语句是一种检查代码中某些内容的方法。如果我们想知道一个数字是否等于 10，我们可以使用 if else 语句。在自然语言中，它听起来像这样；如果这个数字等于 10，那么做一些事情，否则(否则)做其他事情。在代码中，这个语句看起来像这样；

```dart
if (thisNumber == 10) {
     // do something
   } else {
     // do something else
   }
```

如果您尝试将所有内容都读出来，那么我想您会听到它听起来与我们上面的句子非常相似。让我们解释一下。它由五个部分组成：

**If statement：**

```dart
 if (thisNumber == 10)
```


**布尔表达式：**

```dart
thisNumber == 10
```


**操作符**

```dart
==
```

**else 语句(不需要，但有时很有用)**

```dart
else
```

#### If statement：

所以本质上，if 语句就像一个子句。这意味着 if 语句的范围内(大括号“{}”内)的内容不会被调用或不会被执行(执行将是更正确的术语)，除非 if 语句评估为真。评估为真意味着如果 if 语句内(括号“()”内)为真，则执行大括号内的内容。在括号内，我们有所谓的布尔表达式。它可以是真也可以是假。

#### 布尔表达式：

那么，布尔表达式是一个评估结果为真或假的语句。我们的 if 语句也可以像这样；

```dart
bool jonSnowKnowsNothing = true;

if (jonSnowKnowsNothing) {
    print("你知道的越多，不知道的就越多");
  }
```

如果我们运行这段代码，你认为会发生什么？还有打印()；是将括号内的内容打印到控制台/终端的语句。所以如果我们运行这段代码，我们会看到类似的东西

```dart
You know nothing Jon Snow
```

所以我们的程序打印；你知道的越多，不知道的就越多。这样做是因为 if 语句的计算结果为 true。if 语句的计算结果为 true，因为我们将括号内的变量设置为等于 true

```dart
bool jonSnowKnowsNothing = true;
```

如果我们将其设置为等于 false，那么我们的程序将不会打印任何内容，因为我们永远不会进入我们的 if 语句范围。所以实际上这只是为了表明我们的 if 语句检查其中的内容的总和是否为真。我们可以在 if 语句中检查一些事情是否正确。听起来像这样；if Jon Snow knows nothing AND the earth is flat 那么打印这是一个真实的陈述。在代码中看起来像这样

```dart
bool jonSnowKnowsNothing = true; 
bool theEarthIsFlat = true;

if (jonSnowKnowsNothing && theEarthIsFlat) {
     print("This is a true statement");
   }
```

我们的程序现在将打印；这是一个真实的陈述。因为theEarthIsFlat。好的，但是严肃地说，如果我们有一个正确的陈述和一个错误的陈述，那么整个陈述都是不正确的。所以，如果我们再看看上面的例子，它应该是这样的。据我所知，theEarthIsFlat=false，所以如果我们将其设置为假。然后会发生什么？

```dart
bool jonSnowKnowsNothing = true; 
bool theEarthIsFlat = false;

if (jonSnowKnowsNothing && theEarthIsFlat) {
     print("这将是一个疯狂的世界");
   }
```

如果第一个语句为真，第二个语句为真，则我们打印；这将是一个疯狂的世界。但是，如果其中一个语句的计算结果为 false，那么它们都不是 true，这意味着我们被锁定在 if 语句范围之外。

#### 运算符：

让我们来看看我们的布尔表达式运算符。运算符是您在上面看到的“&&”。

```dart
jonSnowKnowsNothing && theEarthIsFlat
```

我们还看到了等号运算符“==”

```dart
thisNumber == 10
```

**比较运算符：**
那么它们是什么以及它们的含义是什么？我们已经看到了等于运算符“==”，它只是检查 a 是否等于 b。为此，a 和 b 必须是相同类型的。您无法将 int 与 String 进行比较。

```dart
int a = 1;
int b = 2;

a == b

// this is false, 1 is not equal to 2
```

这里我们有第二个比较运算符。不等于; !=

```dart
int a = 1;
int b = 2;

a != b

// this is now true, because 1 is not equal to 2
```

如果这很难理解，我建议将其读为“1 不等于 2”。然后你会更容易看出表达式是否正确。例如，如果 a = 1 且 b = 1。你读到“1 不等于 1 是真的吗”。听到这听起来不正确可能更容易。

我们还有大于和小于运算符；> <。

```dart
int a = 1;
int b = 2;

a > b

// this is false, because 1 is not greater than 2
```

很多人一开始都在与这些运算符斗争。我听说过很多解释试图让它更容易。我会给你我的建议如何思考。但是需要注意的是，您编码和使用它们的次数越多，它就会变得越容易。然后你甚至不必考虑。但是，我的建议是简单地将其视为符号的哪一侧最大？

```dart
>
```

左侧较大>右侧较小。我觉得这是最容易记住的方法，因为这也是运算符所做的。它检查什么是最大的。将其视为一侧倾斜的等号。在等号上 = 两行之间的间距相等。但是如果这个标志在一侧倾斜，那么一侧就会有一个巨大的间隙 > 而另一侧没有间隙。

现在我们可以用不同的方式组合这些标志。

```dart
int a = 1;
int b = 2;

a == b // one is equal to two? false
a > b // one is greater than two? false
a < b // one is less than two? true   a != b // one is not equal to two? true   
a !> b // one is not greater than two? true
a !< b // one is not less than two? false
```

这些被称为比较运算符，因为我们比较某些东西。在这种情况下，我们将 a 与 b 进行比较。

**逻辑运算符：**
我们还有第二个类别，即“&&”存在的地方。这些运算符称为逻辑运算符。这些是我们的 AND、OR 和 NOT。看起来像这样；

```dart
&& // AND
|| // OR
! // NOT
```

我们已经看到了“&&”和“！”。我们的 NOT (!) 运算符在解释时可能难以掌握。但是我觉得像上面那样显示时更容易掌握。

```dart
a != b // one is not equal to two? true
a !> b // one is not greater than two? true
a !< b // one is not less than two? false
```

正如您可能会或可能不会看到的那样。我们的 NOT 运算符所做的就是在我们的句子中添加一个“not”。如果我们删除“！” 语句中的运算符。然后我们也简单地将它从我们的句子中删除，我们就恢复正常了。

```dart
// With NOT operator:
   a != b // one is not equal to two? true
   a !> b // one is not greater than two? true
   a !< b // one is not less than two? false

// Without NOT operator:
   a == b // one is equal to two? false
   a > b // one is greater than two? false
   a < b // one is less than two? true
```

好的，这就是我们的 NOT 运算符所做的。现在让我们继续我们的 OR 和 AND 运算符。这些是合乎逻辑的，这意味着它们不像我们之前比较事物的运算符那样工作。逻辑运算符有助于组合多个布尔表达式以创建单个布尔输出。这听起来可能是胡言乱语。别担心，我会解释的。考虑 if 语句的括号，我们检查这里的内容是否为真。这称为评估布尔表达式。我们评估它是否属实。

```dart
if (thisNumber == 10)
```

**AND 运算符：**
现在我们可能要检查几件事。假设我们想创建一个程序，当我们离开家时打开闹钟。我们只希望在房子里没有人的情况下打开警报。否则它会熄灭，那会很烦人。我们是住在这所房子里的两个人。因此，我们只希望在 Jon 和 Khalesi 都不在家的情况下打开警报。所以本质上我们想在 if 语句中检查两件事。我们想看看乔恩是否出局。我们想看看 Khalesi 是否出局。我们的逻辑 AND 运算符可以帮助我们做到这一点。

```dart
if (jonIsOut && khalesiIsOut) {
     print("Alarm truned on");
   }
```

此语句现在检查第一部分的计算结果是否为真。jonIsOut 是真的还是假的？假设他出去了。然后它检查 khalesiIsOut 是真还是假？假设她还在家里。

```dart
bool jonIsOut = true;
bool khalesiIsOut = false;

if (jonIsOut && khalesiIsOut) {
     print("Alarm truned on");
   }
```

由于两者都没有出局，因此整个表达式的计算结果为 false。所以闹钟没有打开。对于我们的 AND 运算符，运算符两侧的两件事都必须评估为真。

**或运算符 ||：**
新示例。假设我们想要构建一个提醒我们去取牛奶的应用程序。如果我们的牛奶盒完全空了，或者我们的牛奶盒剩余的牛奶少于 300 毫升，我们希望收到提醒。因为我们知道每天早上喝 300 毫升。

```dart
double milkLeft = 250.0;

if (milkLeft == 0 || milkLeft < 300) {
     orderMoreMilk();
   }
```

所以这个声明现在为我们解决了这个问题。它会检查牛奶盒是否为空 (milkLeft == 0) 或牛奶盒中的牛奶是否少于 300 毫升 (milkLeft < 300)。在我们的例子中，因为milkLeft = 250，所以第一部分milkLeft == 0 不为真。所以计算结果为假。第二部分milkLeft < 300 为真，因为milkLeft = 250，即小于300。因此，如果任一为真，则该语句为真。因为我们陈述中的一部分是正确的。整个陈述都是真实的。所以我们进入了 if 语句的主体。

#### 其他声明：

现在不需要 else 语句了。您可以只使用 if 语句。您也可以连续使用多个 if 语句

```dart
if (someInput == 2) {

}
if (someInput == 4) {

}
```

在这种情况下，我们将检查变量“someInput”两次。因此，如果 someInput 首先等于 2 然后等于 4，我们将进入这两个 if 语句。 可能会发生这样的情况

```dart
int someInput = 2;

if (someInput == 2) {
     someInput = 4;
} 
if (someInput == 4) {

}
```

首先 someInput 等于 2。所以我们进入第一个 if 语句。然后在第一个 if 语句中，我们将 someInput 设置为等于 4。这意味着当我们的程序进入下一个 if 语句时，我们的变量 someInput 将等于 4。所以我们进入第二个 if 语句。

这是我们很少使用的东西。更常见的是“else if”语句：

```dart
int someInput = 2;

if (someInput == 2) {
     someInput = 4;
} else if (someInput == 4) {

}
```

在这种情况下，我们的程序会同时检查 if 语句，并且只让我们进入从一开始就为 true 的语句。本质上看，someInput 等于 2 吗？那么，someInput 等于 4 吗？根据答案，我们只会进入正确的答案。另一个没有被再次检查。如果我们要检查多个案例，则可以使用此方法。

#### 例子：

假设我们想要创建一个游戏。我们想要创建一个特定的方法，当一个人获胜时调用它。游戏中有不同的层次。根据您的级别，当您获胜时，您将获得特定的奖金。如果你是 1 级，你会得到一袋现金。如果你是二级，你会得到一个新的自定义主题。如果您达到三级(本游戏中的最高等级)，您将获得一个 女朋友。这可能看起来像这样

```dart
void gameWon(int level) {

  if (level == 1) {
    print("你会得到一袋现金");

  } else if (level == 2) {
    print("你会得到一个新的自定义主题");

  } else if (level == 3) {
    print("您将获得一个 女朋友");
    showMeme();

  } else {
    // this would be an error most likely. We would
    // only get here if the player has a level that is
    // greater than 3 or 0. Which should not happen.
    // Since we only have three levels in our game. 
    // A lonely 'else' statement is something that runs
    // if the other if statements are false. Kind of
    // like a safety net.
  }
}
```

看一下代码。我的建议只是考虑一下如何实现第五级。

### 结论：

好吧，这就是 if 语句。



我用的   UI 设计工具 -  [Adobe XD](https://www.adobe.com/se/products/xd.html?gclid=Cj0KCQjwuNbsBRC-ARIsAAzITuetbiiSDrtjLcqNuGk1mn3TJjq7ji0XLa5gLh3kDXVZ7C7cVSPAb_saAiBJEALw_wcB&sdid=B4XQ3XKF&mv=search&ef_id=Cj0KCQjwuNbsBRC-ARIsAAzITuetbiiSDrtjLcqNuGk1mn3TJjq7ji0XLa5gLh3kDXVZ7C7cVSPAb_saAiBJEALw_wcB:G:s&s_kwcid=AL!3085!3!338346976726!e!!g!!adobe xd)