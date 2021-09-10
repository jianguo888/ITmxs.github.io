---
title: "Flutter_crossAxisAlignment与mainAxisAlignment"
date: 2021-08-15T12:20:22+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

在本文中，我们试图消除对这两个主要使用的两个不同小部件中这两个属性的混淆。

那么让我们开始吧

crossAxisAlignment 和 mainAxisAlignment。

那么让我们开始吧

1. **首先是 crossAxisAlignment**，

此属性用于，

> 列小部件作为水平轴。
>
> 行小部件作为垂直轴。

**2. 其次是mainAxisAlignment，**

此属性用于，

> 列小部件作为垂直轴。
>
> 行小部件作为水平轴。

![img](https://miro.medium.com/max/1400/1*N-ooT6NnJOY11r2XJmCSNw.png)

我知道每个人都对为什么这两个属性会随着行和列的两个不同小部件发生变化而感到困惑，

![img](https://miro.medium.com/max/996/1*YCNHjbmEGK4UVVQipNztaQ.gif)

为此，一个简单的答案是，

> **mainAxisAlignment** 是项目在该轴上对齐的方式。
>
> 换句话说，
>
> 孩子应该如何沿主轴放置。

> **crossAxisAlignment**是项目在另一个轴上对齐的方式。
>
> 换句话说，
>
> 如何在 flex 布局中沿交叉轴放置子项。

- **对于小部件属性，**

Column 基本上用于垂直放置的子项。

而 Row 基本上用于以水平方式放置的子项。

所以这就是为什么，

2个动作由这种两个窗口小部件属性(行和列)不同执行。

