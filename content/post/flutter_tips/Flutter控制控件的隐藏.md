---
title: "Flutter控制控件的隐藏"
date: 2021-08-22T13:19:16+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍flutter中如何隐藏/显示控件？

<!--more-->

# flutter中如何隐藏/显示控件？

## 



flutter中提供了Visibility控件实现控件的隐藏或显示，示例代码如下：



控件隐藏：

```dart
Visibility(
  child: Text("不可见"),
  maintainSize: true, 
  maintainAnimation: true,
  maintainState: true,
  visible: false, ), //false 为不可见
```



控件显示：

```dart
Visibility(
  child: Text("显示"),
  visible: true,),
```



也可以通过if条件控制控件的显示或隐藏，代码如下：

```dart
Column(
  children: <Widget>[
    if (show) Text("根据show值显示或隐藏"),
    Text("始终显示"),
  ],)
```