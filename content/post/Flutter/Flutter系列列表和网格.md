---
title: "Flutter系列列表和网格"
date: 2021-08-16T15:53:01+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

![img](https://miro.medium.com/max/1400/1*5fwM69fZ7-R17TZQGgQHtQ.png)



在上一篇关于 Widgets 的文章中，我们遗漏了 Flutter 中一个非常重要的方面：列表。在 Android 中使用 List 或 RecyclerView 意味着编写一个列表项、一个适配器，然后将该适配器附加到布局文件中的视图。这消耗了大量时间，导致开发缓慢。





![img](https://miro.medium.com/max/700/1*FcuzERaVPfWUhbt2IOX3Sw.png)

Android原生列表的典型架构

## Flutter 中的 ListView 介绍

Flutter 中的列表是使用 ListView 小部件创建的。

有两种类型的列表：

1. 用户定义列表中的所有项目(大致相当于 Android 中的 ScrollView)
2. 用户定义了一个在列表中重复的项目(相当于一个 RecyclerView)

**第一类**

第一种类型的列表只是一个可滚动的项目集合，而不是真正的列表。下面是一个 ListView 的使用示例：

```
ListView(
  children: <Widget>[
    Text("Element 1"),
    Text("Element 2")
  ],
),
```

因为用户单独定义列表中的每一项，所以这更像是一个 ScrollView，而不是 Android 中的 ListView 或 RecyclerView。

这种类型只是为了完整性而包含在这里，我们将关注第二种类型的 ListView。

**第二类***



![img](https://miro.medium.com/max/500/1*9KC98cj7J-q0RWKF1qnIww.png)

第二种列表是具有重复块的列表，每个块中的数据不同。这相当于 RecyclerView，但更容易制作。为了制作这样的列表，我们使用 ListView.builder() 构造函数。

让我们看一个包含 Android 版本名称列表的示例 ListView：

```dart
ListView.builder(
      itemBuilder: (context, position) {
        return Card(
          child: Text(androidVersionNames[position]),
        );
      },
      itemCount: androidVersionNames.length,
)
```

androidVersionNames 是所有 android 版本名称的列表。



![img](https://miro.medium.com/max/700/1*_7Nb6Yw9Hj23n3EDfe5F9w.png)

这将向您显示一个(相当不漂亮的)带有 Android 版本名称的卡片列表。

构建器中有两个属性，itemBuilder 和 itemCount。

**itemCount**非常简单，它询问您要在列表中显示多少个重复项。

**itemBuilder**是您返回要显示的项目本身的地方。在这里，我们制作了一张卡片，里面有一个简单的文本小部件。项目构建器需要一个具有上下文和位置参数的 lambda 函数。Position 为您提供它是列表的哪个索引。

总之，当你制作一个列表时，你必须向它提供两件事：1)列表中有多少项目？2)每个项目是什么样子的，它包含什么数据？

**对于 Android 开发者：这完全等同于 RecyclerView。**itemCount 构建 ViewHolder 并将索引数据绑定到它。这样就完成了ViewHolder布局文件的作用和RecyclerView Adapter的绑定功能。因为没有单独的布局文件，所以也没有 LayoutManager 和适配器的实例化。

与创建列表的旧方法相比，这确实是革命性的。

## 在 Flutter 中创建网格

网格与列表非常相似。我们使用的小部件是 GridView.builder 而不是 ListView.builder。

下面是一个 GridView 的例子：

```
GridView.builder(
    itemBuilder: (context, position) {
      return Card(
        child: Text(androidVersionNames[position]),
      );
    },
    itemCount: androidVersionNames.length,
    gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 2),    
)
```

在网格中，唯一的根本区别是它有多列。因此，GridView 还采用 gridDelegate 来帮助您设置列数。

(gridDelegate 这个名字给了我强烈的 Java 怀旧之情)

gridDelegate 中的 crossAxisCount 是列数。这也可以根据方向改变。

注意：您可以根据索引在列表或网格中提供多种类型的项目。这在 Flutter 中更容易，因为它没有像原生 Android 那样创建和绑定数据的不同方法。前面提到的“一种物品”只是为了简单起见。

## 更高级的例子

为了理解一个真实的例子，这里有一个用于电子邮件应用程序的更好看的演示 ListView。



![img](https://miro.medium.com/max/700/1*jZuoycYSk396u2kj48EbQQ.png)

```
ListView.builder(
  itemBuilder: (context, position) {
    return Column(
      children: <Widget>[
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: <Widget>[
            Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: <Widget>[
                Padding(
                  padding:
                      const EdgeInsets.fromLTRB(12.0, 12.0, 12.0, 6.0),
                  child: Text(
                    sendersList[position],
                    style: TextStyle(
                        fontSize: 22.0, fontWeight: FontWeight.bold),
                  ),
                ),
                Padding(
                  padding:
                      const EdgeInsets.fromLTRB(12.0, 6.0, 12.0, 12.0),
                  child: Text(
                    subjectList[position],
                    style: TextStyle(fontSize: 18.0),
                  ),
                ),
              ],
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: Column(
                mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                children: <Widget>[
                  Text(
                    "5m",
                    style: TextStyle(color: Colors.grey),
                  ),
                  Padding(
                    padding: const EdgeInsets.all(8.0),
                    child: Icon(
                      Icons.star_border,
                      size: 35.0,
                      color: Colors.grey,
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
        Divider(
          height: 2.0,
          color: Colors.grey,
        )
      ],
    );
  },
  itemCount: sendersList.length,
),
```

随意探索更多并尝试创建自己的布局。

Flutter 的 ListViews 超越了原生移动开发提供的功能，并在大量可定制的同时大大节省了开发时间。

