---
title: "Flutter ListView 和ScrollPhysics详细介绍"
date: 2021-08-16T15:31:47+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---





前段时间，我写了一篇关于[在 Flutter 中使用 ListView 和 GridView](https://medium.com/@dev.n/the-complete-flutter-series-article-3-lists-and-grids-in-flutter-b20d1a393e39)的[基础知识](https://medium.com/@dev.n/the-complete-flutter-series-article-3-lists-and-grids-in-flutter-b20d1a393e39)的文章。本文旨在对 ListView 类、ScrollPhysics 以及通用小部件的调整和优化进行更详细的探索。

Flutter 中的 ListView 是可滚动项的线性列表。我们可以使用它来制作可滚动的项目列表或制作重复项目的列表。

# 探索 ListView 的类型

我们将从查看 ListViews 的类型开始，然后查看其他功能和对它的巧妙修改。

让我们看看有哪些 ListViews 类型：

1. **ListView**
2. **ListView.builder**
3. **ListView.separated**
4. **ListView.custom**

让我们一一探索这些类型：

## 列表显示

这是 ListView 类的默认构造函数。ListView 只需要一个子项列表并使其可滚动。

![img](https://miro.medium.com/freeze/max/17/1*HCj38H38dE2u_GKBE6gNjQ.gif?q=20)

![img](https://miro.medium.com/max/700/1*HCj38H38dE2u_GKBE6gNjQ.gif)

使用默认构造函数构造的 List

代码的一般格式为：

```
ListView( 
  children: <Widget>[ 
    ItemOne(), 
    ItemTwo(), 
    ItemThree(), 
  ], 
),
```

通常这应该与**少量子**元素一起使用，因为 List 也会构造列表中的不可见元素，而大量元素可能会导致效率低下。

## ListView.builder()

builder() 构造函数构造一个重复的项目列表。构造函数有两个主要参数：一个**itemCount**表示列表中的项目数，一个**itemBuilder**用于构造每个列表项。



![img](https://miro.medium.com/max/700/1*9eJ11a04kjjUnmjNFEq5zw.gif)

使用 builder() 构造函数构造的 List

代码的一般格式为：

```
ListView.builder( 
  itemCount: itemCount, 
  itemBuilder: (context, position) { 
    return listItem(); 
  }, 
),
```

列表项是**惰性构建的**，这意味着**构建**特定数量的列表项，当用户向前滚动时，较早的列表项将被销毁。

**巧妙的技巧**：由于元素是延迟加载的，并且只加载了所需数量的元素，我们真的不需要 itemCount 作为强制参数，并且列表可以是**无限的**。

```
ListView.builder( 
  itemBuilder: (context, position) { 
    return Card( 
      child: Padding( 
        padding: const EdgeInsets.all(16.0), 
        child: Text(position.toString(), style: TextStyle(fontSize: 22.0),), 
      ), 
    ); 
  }, 
),
```

![img](https://miro.medium.com/freeze/max/17/1*oisoTExGF0xjYrcSX1iIxA.gif?q=20)

![img](https://miro.medium.com/max/700/1*oisoTExGF0xjYrcSX1iIxA.gif)

没有 itemCount 参数的 ListView

## ListView.separated()

在**separator** () 构造函数中，我们生成一个列表，我们可以**指定**每个项目之间**的分隔符**。

![img](https://miro.medium.com/freeze/max/17/1*iDYxUys18QoFGzAx_ebr4A.gif?q=20)

![img](https://miro.medium.com/max/700/1*iDYxUys18QoFGzAx_ebr4A.gif)

使用 ListView.separated() 构造函数构造的 ListView

本质上，我们**构造了两个交织的列表**：一个作为主列表，一个作为分隔列表。

请注意，此处不能使用前面构造函数中讨论的无限计数，并且此构造函数强制使用 itemCount。

这种类型的代码如下：

```
ListView.separated( 
      itemBuilder: (context, position) { 
        return ListItem(); 
      }, 
      separatorBuilder: (context, position) { 
        return SeparatorItem(); 
      }, 
      itemCount: itemCount,
 ),
```

这种类型的列表允许您动态定义分隔符、为不同类型的项目设置不同类型的分隔符、在需要时添加或删除分隔符等。

此实现还可用于轻松**插入其他类型的元素**（例如广告），无需对列表项中间的主列表进行任何修改。



![img](https://miro.medium.com/max/700/1*e6LEilKRr0p62g7VNat3Bw.png)

示例显示位置可被 4 整除时的广告

**注意**：分隔符列表长度比项目列表**少**1，因为在最后一个元素之后不存在分隔符。

## ListView.custom()

custom() 构造函数顾名思义，允许您使用自定义功能构建 ListViews，以了解如何构建列表的子项。为此所需的主要参数是构建项目的 SliverChildDelegate。SliverChildDelegates 的类型是

1. **SliverChildListDelegate**
2. **SliverChildBuilderDelegate**

SliverChildListDelegate 接受子项的直接列表，而 SliverChildBuiderDelegate 接受 IndexedWidgetBuilder（我们使用的构建器函数）。

您可以使用或子类化这些来构建您自己的delegates。

**ListView.builder 本质上是一个带有 SliverChildBuilderDelegate 的 ListView.custom。**

**ListView 默认构造函数的行为类似于带有 SliverChildListDelegate 的 ListView.custom。**

现在我们已经完成了 ListViews 的类型，让我们来看看 ScrollPhysics。

# 探索 ScrollPhysics

为了控制滚动发生的方式，我们在 ListView 构造函数中设置了**物理**参数。不同类型的物理是：

## NeverScrollableScrollPhysics

NeverScrollableScrollPhysics**呈现列表不可滚动**。使用它可以完全禁用 ListView 的滚动。

## BouncingScrollPhysics

当列表结束时，BouncingScrollPhysics 将**返回**列表。iOS 上也使用了类似的效果。



![img](https://miro.medium.com/max/700/1*8jjtR8LTpfqQ6fd6sGX9jA.gif)

## ClampingScrollPhysics

这是 Android 上使用的默认滚动物理。列表在结尾处停止并给出指示它的效果。



![img](https://miro.medium.com/max/700/1*6MzFiV-QUtu-VBCWO2uTKA.gif)

## FixedExtentScrollPhysics

这与此列表中的其他稍有不同，因为它仅适用于 FixedExtendScrollControllers 和使用它们的列表。例如，我们将采用一个 ListWheelScrollView 来制作一个类似轮子的列表。

FixedExtentScrollPhysics**只滚动到**items而不是之间的任何偏移。



![img](https://miro.medium.com/max/700/1*4XETw8zoeNGGODA77c09Iw.gif)

这个例子的代码非常简单：

```dart
FixedExtentScrollController fixedExtentScrollController =
    new FixedExtentScrollController();
ListWheelScrollView(
  controller: fixedExtentScrollController,
  physics: FixedExtentScrollPhysics(),
  children: monthsOfTheYear.map((month) {
    return Card(
        child: Row(
      children: <Widget>[
        Expanded(
            child: Padding(
          padding: const EdgeInsets.all(8.0),
          child: Text(
            month,
            style: TextStyle(fontSize: 18.0),
          ),
        )),
      ],
    ));
  }).toList(),
  itemExtent: 60.0,
),
```

# 还有一些事情要知道

## 如何在列表中保持被销毁的元素？

Flutter 提供了一个 KeepAlive() 小部件，它使一个项目保持活动状态，否则该项目将被销毁。在列表中，元素默认包装在 AutomaticKeepAlive 小部件中。



![img](https://miro.medium.com/max/700/1*79yr2xp2WFrnIsRBnjtIkw.png)

可以通过将 addAutomaticKeepAlives 字段设置为 false 来禁用 AutomaticKeepAlives。这在元素不需要保持活动状态或 KeepAlive 的自定义实现的情况下很有用。

## 为什么我的 ListView 在列表和外部小部件之间有空间？

默认情况下，ListView 在它和外部小部件之间有填充，要删除它，请将填充设置为 EdgeInsets.all(0.0)。



这就是这篇文章！我希望你喜欢它，
