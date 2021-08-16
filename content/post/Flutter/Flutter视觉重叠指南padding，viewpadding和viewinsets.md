---
title: "Flutter视觉重叠指南padding，viewpadding和viewinsets"
date: 2021-08-16T15:41:43+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

![img](https://miro.medium.com/max/4167/1*2YGkZRsdzjXFkEoiPtu03A.png)

Flutter 旨在运行在各种设备上。而在多个设备上运行就是适应每个设备的怪癖和功能。

我们开发人员需要注意的一种设备怪癖是与我们的应用程序重叠的系统 UI。它可能是状态栏、凹口或底部指示器。

幸运的是，Flutter 支持开箱即用的`MediaQuery`. 您可以使用 获取状态栏高度`MediaQuery.of(context).padding.top`，使用`MediaQuery.of(context).viewInsets.bottom`获取键盘高度等。

不过，`MediaQuery`本身也有一些瑕疵。例如，它有`padding`但也有一个`viewPadding`属性。有时`EdgeInsets.zero`即使有可见的模糊系统 UI，它们也会返回。

因此，在本文中，我将尝试为澄清问题。

对于本文的其余部分，每当我指的是`padding`，`viewPadding`或者`viewInsets`，它的`MediaQuery`是我说的。

# 分类

首先，我们应该将系统晦涩的 UI 分为两类：

**部分模糊的用户界面**

这些是状态栏、底部指示器。我们应该在我们的应用程序中考虑这些 UI 以创建身临其境的体验。

**完全模糊的用户界面**

这些通常是键盘。我们应该调整我们的布局以避免出现这些 UI。



![img](https://miro.medium.com/max/700/1*PJs7Qkq3ged7n1lwl0pnfg.png)

> 请注意，当键盘出现时，底部指示器部分从部分变为完全模糊的 UI。

# Flutter 如何处理这个：`viewInsets`和`viewPadding`

Flutter`MediaQuery`将它们分为 2 个属性：

`viewInsets`→**完全**模糊的 UI所需的空间。

`viewPadding`→**部分**被遮挡的 UI所需的空间。



![img](https://miro.medium.com/max/700/1*Tr01W2mfTFuoJ96YUCx6Eg.png)

这2个是相互***独立\***的。当键盘出现时，`viewInsets.bottom`从 0 变为 336，但`viewPadding.bottom`保持在 34，尽管我们可以看到键盘消耗了我们的应用程序占用的底部指示器空间。

因此，考虑上面**完全**模糊的 UI 消耗**部分**模糊的 UI 的情况，Flutter 有一个属性可以解释消耗，而它正是`padding`!

`padding`→ 将完全遮挡的部分考虑在内后仍被系统 UI 部分遮挡的显示部分。它是这样计算的：

> `padding`= max(0, `viewPadding`- `viewInsets`)



![img](https://miro.medium.com/max/700/1*agZ1IxXB-6gPf55vSG3RCA.png)

我们可以看到，`viewInsets`相当简单，但`padding`和`viewPadding`有时可以互换使用，但是有时候这样做是不明智的。

# 明智地选择填充：何时使用 viewPadding 和 padding



![img](https://miro.medium.com/max/700/1*WHCoOC-nPl3C0ihYxk7WBQ.png)

**布局 1**

我们决定我们希望我们的按钮留在键盘后面。

- 我们需要设置我们的`Scaffold.resizeToAvoidBottomInset = false`以防止我们`Scaffold`调整大小。
- 对于这种情况，我们应该使用，`viewPadding`因为那时我们的按钮与流程无关，使用`padding`可能会导致我们的布局跳下来。

**布局 2**

我们决定我们希望我们的按钮在键盘出现时转换并保持在顶部。

- 我们需要检查键盘是否可见使用 `viewInsets.bottom > 0`
- 对于这种情况，我们应该使用，`padding`因为 Flutter 会考虑`viewInsets`并减少`padding`到 0。我们的按钮将很好地保持在顶部，无需任何进一步检查。

# 为什么我的 viewPadding / padding / viewInsets 全为零？

我之前说过我们可以检查是否`viewInsets.bottom > 0`确定键盘是否可见。这只是部分正确。

`MediaQuery.of(context).viewInsets`在错误的地方打电话，`EdgeInsets.zero`即使键盘清楚地显示，我们也只会得到。

错误的地方是什么意思？

在颤振，一`Widget`，平时`Scaffold`，可以消耗`viewPadding`，`padding`或者`viewInsets `和修改`MediaQuery`，然后传递到它的孩子。

这个想法是，如果父母照顾了被遮挡的 UI，那么孩子就不应该再担心它们了。

![img](https://miro.medium.com/max/30/1*ZLjIc8fD5SPR2cMGf2PQbA.png?q=20)

![img](https://miro.medium.com/max/700/1*ZLjIc8fD5SPR2cMGf2PQbA.png)

如果`Scaffold`有`AppBar`，它将消耗`viewPadding.top`。

如果`Scaffold`有`BottomNavigationBar`，它将消耗`viewPadding.bottom`。

如果 Scaffold 有`resizeToAvoidBottomInset = true`，它会消耗`viewInsets.bottom`.

所以，如果你想检查键盘知名度，检查它们**之外**的`Scaffold`，使用一个`InheritedWidget`传递下来，如果需要的数据。

# 为 Android Q 做准备：systemGestureInsets

顺便提一下，`MediaQuery`还有一个名为 的属性`systemGestureInsets`。这是针对 Android Q 的手势导航。

# 总结

- `viewPadding` 用于部分模糊的 UI
- `viewInsets` 用于完全模糊的 UI
- `padding`派生自`viewPadding`并且`viewInsets`应该仅在必要时使用。
- 有些`Widgets`可以使用这些属性并将修改后的属性传递`MediaQuery`给他们的孩子。
