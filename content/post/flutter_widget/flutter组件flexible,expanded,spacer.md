---
title: "Flutter组件flexible,expanded,spacer"
date: 2021-08-23T08:38:28+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]
---

所以说 `Expanded` 就是一个强制延展的 `Flexible` 组件。

`fit` 是一个 `FlexFit` 型的枚举，只有两个元素 `tight` 和 `loss` ，所以并不是很难。默认下是 `loss` 。

```dart
enum FlexFit {
  tight,
  loose,
}

```

那  `tight` 和 `loss` 有什么作用呢？我们通过之前的例子再看一下：如果 `Flexible` 包裹的子组件有固定的尺寸，默认情况下 `loss` 是无法使其区域延展的，甚至 `Flexible` 本身的尺寸也不会扩展。




两者在使用上并没有什么区别，由于 `Flexible` 可以设置 `fit` 值，所以用途要比 `Expanded` 广泛。而强制延展的场景使用 `Expanded` 组件语义更好，而且简单一点。

`Spacer` 的实现也非常简单，下面是它的全部代码。它是一个 `StatelessWidget` ，内部依赖 `Spacer` 组件实现功能，特点是：它不能设置子组件，本身作为空白占位使用。

####  认识 Card 组件

卡片效果作为 `Material Design` 中的一员，Flutter 中 `Card` 组件自然是要有的。源码注释中是这么描述它的：`带有轻微圆角和立面阴影的面板。`

通过 `shadowColor` 可以设置阴影的颜色，通过 `elevation` 可以设置阴影的深度。

#####  clipBehavior 裁剪行为

`Clip` 是一个枚举类，包含四种形式，如下：

```dart
enum Clip {
  none, // 无
  hardEdge, // 硬边缘
  antiAlias, // 抗锯齿
  antiAliasWithSaveLayer, // 抗锯齿保存图层
}

```

在内容的容器中使用图片装饰，你会很疑惑，为什么没有圆角了。因为 `Card` 的默认裁剪行为为 `Clip.none`。这时需要通过指定 `clipBehavior` 完成圆角，这是一个小细节，不知道的话很可能觉得 Card 组件不好用。

也许你会觉得默认的圆角有点小，想要变大点，或不喜欢圆角装饰，先要搞点创造性装饰，那么 `shape` 属性将为你打开一扇大门。需要的是一个 `ShapeBorder` 对象，由于其为抽象类，需要找它的子类，

比如想要增加圆角，可以使用 `RoundedRectangleBorder` 形状。

如果你将 `InkWell` 放在了 `Center` 之上，那么它水波纹会被前景所覆盖

正确的使用方式是在 child 组件上嵌套 `InkWell`。

有些时候，比如使用 `Image`、或为 `Container` 设置颜色、装之后，水波纹就无法触发。

这是可以通过 `Ink` 组件来替代 `Container` 或 `Image` 源码中是怎么说的：



##### 1.SizedBox 基本信息

下面是 `SizedBox` 组件类的`定义`和 `构造方法`，可以看出它继承自 `SingleChildRenderObjectWidget`。可接受一个子组件，和区域的宽高。

任何组件的`占位区域`、`绘制内容`最终都取决于 `RenderObject` 。而并非所有的组件都和  `RenderObject` 有关，只有 `RenderObjectWidget` 负责维护 `RenderObject` 。像 `StatelessWidget` 和 `StatefulWidget` 这种都是基于已有组件进行组合，往深层去看，他们都是基于某些 `RenderObjectWidget` 实现。

关于布局， `RenderObject` 有一个非常重要的属性: `Constraints` 类型的 `constraints` ，表示自身受到的区域约束限制。而 `RenderBox` 作为 `RenderObject` 的子类，拓展出了 `size` 的概念，绝大多数组件维护的`渲染对象`都是在 `RenderBox` 基础上进行拓展的。

`SizedBox` 外层通过 `ConstrainedBox` 组件添加添加一个 `[w(20,20) - h(20,20)]` 的强制约束。可以看出即使 `SizedBox` 设置了固定的宽高，但是在外层的约束之下，会优先满足父级约束。

[推论1] SizedBox 的最终尺寸会受到父级约束的影响，并非一定为指定值。

####  认识 ConstrainedBox 组件

源码中对 `ConstrainedBox` 的介绍为：为子组件施加`额外的`约束。

##### 2.认识约束  BoxConstraints

`BoxConstraints` 类是对区域范围的抽象，维护着四个值: 这四个值组成了一个`尺寸的取值区域`，来限定子组件的尺寸大小。这里再强调一下，组件本身是没有尺寸概念的，这里说的组件尺寸，是指其维护的`渲染对象`尺寸。

| 成员对象  | 对象类型 | 默认值          | 介绍           |
| --------- | -------- | --------------- | -------------- |
| minWidth  | double   | 0               | 尺寸宽度最小值 |
| maxWidth  | double   | double.infinity | 尺寸宽度最大值 |
| minHeight | double   | 0               | 尺寸高度最小值 |
| maxHeight | double   | double.infinity | 尺寸高度最大值 |

##### . BoxConstraints 的构造方法

`BoxConstraints` 一共有 `6` 种构造方法，普通构造传入四个值，之前看过了。

`.tight` 构造 ，需要传入一个 `Size` 对象，将约束区域为`指定宽高` , 和 `SizedBox` 作用是一致的。

`.tightFor` 构造, 传入的宽高值。和 `tight` 不同的是这里`宽/高`可谓空，如果宽为空则宽度取值范围在 `0~无限` 之间，高度也类似`SizedBox` 的内部就是使用 `.tightFor` 根据宽高构造约束对象的。也就是说，你使用 `tightFor` 创建约束，用于 `ConstrainedBox` 中，本质上和 `SizedBox` 一样。

`.expand` 构造，传入宽和高。和 `tightFor` 类似，`宽/高`可空。不同点在于：如果宽为空，则宽度取值范围在 `无限~无限` 之间，也就是说区域无限，会尽可能扩充。

`.loose` 构造，和`.tight` 类似 ，需要传入一个 `Size` 对象。可以看出下界是 `0` ，上界是 `size` 的宽高。所以这种的约束是松散的，并不像 `.tight` 会将宽高定死。

`.tightForFinite` 构造，默认宽高无限。拿宽举例，默认情况下 `width`无限，取值区间为 `0~无限`，如果传入的 `width` 非无限，那么宽度将被固定为 `width` ，高度也类似。

























19.20.21.22

