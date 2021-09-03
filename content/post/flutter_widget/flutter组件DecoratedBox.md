---
title: "Flutter组件DecoratedBox"
date: 2021-08-31T09:32:43+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

#### 1.认识 DecoratedBox 组件

`DecoratedBox` 组件可能单独使用的频率不是很高，因为它被集成在了 `Container` 组件中，但装饰的使用方式是共通的，源码中说 `DecoratedBox` 会在其孩子的前景或背景上绘制 `Decoration` 装饰对象。这说明 `Decoration` 才是装饰的重点，我们需要了解或自定义 `Decoration`。


下面是 `DecoratedBox` 组件类的`定义`和 `构造方法`，可以看出它继承自 `SingleChildRenderObjectWidget` 。构造时必须传入尺寸 `decoration` 参数，可以传入 `position` 入参。

![image-20210831093750453](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210831093750453.png)

`position` 成员的类型是 `DecorationPosition` 枚举，表示在前景绘制还是在背景绘制。

![image-20210831094012595](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210831094012595.png)

```
DecoratedBox(
  decoration: BoxDecoration(
      color: Colors.orangeAccent,
      borderRadius: BorderRadius.only(
          topLeft: Radius.circular(20),
          bottomRight: Radius.circular(20),
        bottomLeft: Radius.circular(5),
        topRight: Radius.circular(5),
      ),
  ),
  child: buildContent(),
);

Widget buildContent() {
  return SizedBox(
    width: 80,
    height: 80,
    child: Icon(Icons.android, size: 50, color: Colors.white),
  );
}

```





```
BoxDecoration(
    color: Colors.transparent,
    borderRadius: BorderRadius.only(
      topLeft: Radius.circular(20),
      bottomRight: Radius.circular(20),
      bottomLeft: Radius.circular(5),
      topRight: Radius.circular(5),
    ),
    border: Border.all(color: Colors.red, width: 2),
    image: DecorationImage(
      image: AssetImage('assets/images/bg_6.jpeg'),
      fit: BoxFit.cover,
    ),
    boxShadow: [
      const BoxShadow(
          color: Colors.red,
          offset: Offset.zero,
          blurRadius: 2,
          spreadRadius: 2),
    ]
),


```

