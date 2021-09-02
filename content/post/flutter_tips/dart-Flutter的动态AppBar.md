---
title: "Dart Flutter的动态AppBar"
date: 2021-09-02T13:48:00+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

我必须动态更改AppBar的外观和项目(通过点击另一个UI元素)。
最好的方法是什么？

我测试了几种方法。例如，

```dart
return Scaffold(
      appBar: StreamBuilder(
          stream: bloc.tasks,
          builder: (context, AsyncSnapshot<List<UserTask>> tasks) {
            return new AppBar();/// my setup is here
          }),
```


但这显然无法编译。



**最佳答案**

`appBar`需要一个实现`PreferredSizeWidget`的小部件，而`StreamBuilder`不是一个。

您可以将该树包装成`PreferredSize`:

```dart
Scaffold(
  appBar: PreferredSize(
    preferredSize: const Size(double.infinity, kToolbarHeight),
    child: // StreamBuilder
  ),
)
```
