---
title: "Flutter使用Spread减少代码行"
date: 2021-08-19T23:34:21+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
 
---

> 本文主要介绍

<!--more-->

https://tsvillain.medium.com/flutter-reduce-line-of-code-using-spread-8d6988bac0d

# Flutter 使用 Spread (...) 减少代码行

吨展开运算符是一种有用且快速的语法，用于将项添加到数组、组合数组。

但是今天我们将学习如何在 UI 代码中使用扩展运算符 (...) 来减少代码行。

1. **基本方法**

![img](https://miro.medium.com/max/1400/1*tUqJQLpqHyzQXv0EMT_WkA.png)

基本方法

正如您在上面的代码中看到的，我们有一列包含一个 Button 和 5 个 ListTile。

> 现在我们的任务是减少这种重复的线条。

2.**嵌套列(一般方法)**

通常我们遇到过这样的事情，我们在 Column 中使用一个 Column 并将一个 List 传递给它的孩子。

这很好，但我们可以从 90% 到 100%。



![img](https://miro.medium.com/max/875/1*D1EQCwj5yzE_Fdf2qnPN5w.png)

嵌套列

3.**传播 (…) 方法**

Spread o perator 使用起来很简单，只需添加 ... 是开始，您就可以开始了。

但是你我在想为什么它没有抛出错误。这很简单，因为 Column Contains a List<Widget> 并且可以使用扩展运算符将现有 List 添加到它接受并理解的另一个 List 中。



![img](https://miro.medium.com/max/875/1*LYdurecrfDeqZN1X9lkZNw.png)

使用扩展运算符

4.**更多使用Spread**

您会想从 90% 到 100% 有什么意义，因为很少有新开发人员会对这种语法感到困惑。

因此，让我们检查下面的示例，其中我在具有扩展的列中使用 if 条件，它有条件地呈现小部件，这比使用带有三元运算符的嵌套列更好

![img](https://miro.medium.com/max/875/1*A0dfqfxQanSS1NVQETe5DQ.png)

Spread Operator with If Condition

That’s it for this Article but there is no limitation of using this teeny-tiny operator in numerous places.

这就是本文的内容，但在许多地方使用这个极小的运算符没有限制。