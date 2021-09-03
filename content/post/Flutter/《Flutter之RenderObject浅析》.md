---
title: "《Flutter之RenderObject浅析》"
subtitle: ""
date: 2021-07-31T21:05:45+08:00
lastmod: 2021-07-31T21:05:45+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---

> 本文主要介绍Flutter 开发指南中提到 Widget 是 Android View 的对等物。虽然形象，但不准确。Flutter 文档说一切皆 Widget，同时又说 Widget 只是 UI 配置/蓝图，到底该如何理解？本文带你揭开 Widget 的神秘面纱，探寻背后藏着的 RenderObject。

<!--more-->

# 引言

> in Flutter, the rough equivalent to a View is a Widget。Flutter Widget 是 Android View 的对等物
> [参考](https://flutter.dev/docs/get-started/flutter-for/android-devs#what-is-the-equivalent-of-a-view-in-flutter)

当你使用 Flutter 一段时间后你会发现两者并不完全对等。比如，Android 中这些我们熟悉的用于操作 View 的方法，在 Flutter Widget 中并不存在。

```
View.getWidth();
View.getHeight();
View.getLocationInWindow();
ViewGroup.addView();
ViewGroup.removeView();
```

> Flutter 中 一切 UI 皆 Widget。不过，Widget 生而不同
> [参考](https://flutter.dev/docs/resources/technical-overview#everythings-a-widget)

不同种类的 Widget 存在着非常大的差异，包括：

- 功能差异
- 类结构差异
- 树结构差异

## 功能差异

以 Flutter 生成的缺省工程为例，UI 截图 和 Widget Outline 分别如下：

![StatelessWidget w600](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15773323688839.jpg)

从上图看 Widget 相当强大，可以通过 StatelessWidget 和 StatefulWidget 将已有的各种 Widget **组合**成复杂的界面，但即便如此，StatelessWidget 和 StatefulWidget 等 Widget 实际上并没有任何**自定义绘制** 能力(它们似乎充当了 Android ViewGroup 的角色)。

![ErrorWidget -w300](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15773505764400.jpg)

相反，Opacity、ErrorWidget 等 Widget 没有强大的组合能力，却有**自定义绘制能力**(它们更像是 Android View)。

所以我们可以根据自定义绘制能力将它们粗略分成两大类：**只支持组合**与**可自定义绘制**。追根溯源我们将发现这种不同其实来自于 RenderObject。

## 类结构差异

![Widget的类结构 -w800](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15773503347545.jpg)

观察不同 Widget 的类结构，不难发现 Opacity 和 ErrorWidget 这些可自定义绘制的控件与 StatelessWidget 和 StatefulWidget 这些不支持自定义绘制的控件不同，前两者并非直接继承自 `Widget`，而是继承自 `RenderObjectWidget`。

看名字，不难发现 `RenderObjectWidget` 跟 RenderObject 存在某种关联。

## 树结构差异

```
void main() {
    runApp(Container(child: ErrorWidget('hello, world')));
}
```

![渲染树与RenderObject -w600](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15773516364547.jpg)

同样是 Widget，渲染树中可以看到 ErrorWidget 有一个关联的 renderObject 对象，而 Container 并没有关联的 renderObject 对象。产生这种差异的原因是什么？

# RenderObject 介绍

## Widget，Element 与 RenderObject

Element 是联系 Widget 和 RenderObject 的纽带。

- Widget - 存放渲染内容、视图布局信息
- Element - 存放上下文信息，通过 Element 遍历视图树，Element 同时持有Widget和RenderObject
- RenderObject - 根据 Widget 的布局属性进行 layout，对 widget 传入的内容进行渲染绘制

先来快速地了解一下。

首先通过一张图来看 Element 是如何发挥其纽带作用的：

![Widget,Element与RenderObject -w800](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15773523142153.jpg)

- 每个 Widget 会创建一个对应的 Element 对象 (通过 `Widget.createElement()`)
- 每个 Element 会持有对应 Widget 对象的引用 (注意 `createElement()` 方法第一个参数)
- RenderObjectElement 是 Element 的子类，这种 Element 持有一个 RenderObject 对象的引用

其次，Element 也是树形结构。我们常说 Widget 是配置/蓝图，其实更具体来说 Widget 是 Element 的配置/蓝图。

![Element数据结构 -w800](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15774111904123.jpg)

配置(Widget)的变更导致 Element 树进行相应地更新。`Element.updateChild()` 是 Widget 系统的核心方法，它负责处理这个更新。

`Element.updateChild()` 的更新策略见下图：

![Element.updateChild() -w800](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15774121792952.jpg)

注意区分 Widget 的更新和 Element 的更新有着非常重大的差别：

- Widget 是配置数据，是轻量级对象。Widget 的更新对应着 `Stateless.build()` 和 `StatefulWidget.build()`，**重新创建整个 Widget 树**，是个全量过程
- Element 是重量级对象。Element 的更新对应着 `Element.updateChild()`，**更新整个 Element 树**，是个增量过程

最后，如果非要找一段代码来体现三者之间的联系，非 `Element.inflateWidget()` 方法莫属。该方法简化后的代码如下，完整代码见 [Github](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L3247)。

```
Element inflateWidget(Widget newWidget, dynamic newSlot) {
  final Element newChild = newWidget.createElement();
  newChild.mount(this, newSlot);
  return newChild;
}
```

如果上述代码中的 Element 和 Widget 实际类型分别是 `RenderObjectElement` 和 `RenderObjectWidget`，则 `mount()` 方法会创建 `renderObject`。

```
void mount(Element parent, dynamic newSlot) {
  super.mount(parent, newSlot);
  _renderObject = widget.createRenderObject(this);
  ...
}
```

![RenderObject创建流程 -w600](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15773529078805.jpg)

注意：只有 RenderObjectElement/RednerObjectWidget 的子类才能创建 RenderObject 对象。

了解 Widget，Element 与 RenderObject 三者之间关系后我们再来重点看 RenderObject。

## 初识 RenderObject

> The Flutter render tree is a low-level layout and painting system based on a retained tree of objects that inherit from RenderObject. Most developers using Flutter won’t need to interact directly with the rendering tree. Instead, most developers should use widgets, which are built using the render tree.
> The base class for every node in the render tree is RenderObject , which defines the base layout model
> [参考](https://flutter.dev/docs/resources/rendering#introduction)

> The RenderObject class hierarchy is the core of the rendering library’s reason for being.
> The RenderObject class also implements the layout and paint protocols.
> [参考](https://api.flutter.dev/flutter/rendering/RenderObject-class.html)

翻译过来就是：

- RenderObject 类层级是渲染库的核心，定义了基本的布局模型
- RenderObject 类实现了布局和绘制协议
- RenderObject 及其子类是渲染树(低层布局和绘制系统)的基础
- 开发者应当使用 Widget 来构建渲染树，而不是直接操作渲染树

## RenderObject 结构

RenderObject 的结构如下：

```
abstract class RenderObject extends AbstractNode {
  /// The parent of this node in the tree.
  AbstractNode _parent;
  
  /// Data for use by the parent render object.
  ParentData parentData;

  /// The layout constraints most recently supplied by the parent.
  Constraints _constraints;
  
  /// The owner for this node (null if unattached).
  Object _owner;
 
  /// Whether this node is in a tree whose root is attached to something.
  bool get attached => _owner != null;

  // 注意: 这个方法需要子类正确重写
  /// Override to setup parent data correctly for your children.
  void setupParentData(covariant RenderObject child) {}

  /// Called by subclasses when they decide a render object is a child.  
  void adoptChild(RenderObject child) {}
  /// Called by subclasses when they decide a render object is no longer a child.  
  void dropChild(RenderObject child) {}
  
  /// Mark this node as attached to the given owner.  
  void attach(PipelineOwner owner) {}
  /// Mark this node as detached.
  void detach() {}
}
```

可以将 RenderObject 的属性及相关方法分成三类：

|                | 属性                                      | 方法                         |
| -------------- | ----------------------------------------- | ---------------------------- |
| 与 parent 交互 | **_parent**, **parentData**, _constraints | setupParentData()            |
| 与 owner 交互  | _owner                                    | attach(), detach(), attached |
| 与 child 交互  | -                                         | adoptChild(), dropChild()    |

RenderObject 中两个重要的属性是 `_parent` 和 `parentData`。

- `_parent` 用于将各 RenderObject 组合成一棵树
- `parentData` 是个比较神奇的存在。(不过，回想一样 `View.mLayoutParams` 字段就不觉得神奇了)。逻辑上讲，这个字段的所有者是当前 RenderObject 的 parent；物理上讲，这个字段被保存在当前 RenderObject。这个字段通常保存类似于子节点位置这样的信息。

简单来说，RenderObject 定义了**布局/绘制协议**，但并没定义具体**布局/绘制模型**。(RenderBox 是 RenderObject 的子类，它遵守 RenderObject 定义的布局/绘制协议，实现了一个具体的布局/绘制模型。)

什么是布局/绘制协议？举例来说，我们自己继承 RenderObject 实现一个新的子类时，必须遵守一系列的要求(完整协议见 [文档](https://api.flutter.dev/flutter/rendering/RenderBox-class.html))：

- 要实现 `performLayout()` 方法，该方法接受一个 `Constraints` 参数
- 要实现 `paint()` 方法
- 使用 `dropChild()` 方法添加子节点
- 使用 `adoptChild()` 方法移除子节点
- …

这些要求就是协议。Android 中也有类似”协议” (或称之为约定 Contract)，比如覆盖 `View.onMeasure()` 方法时要求必须调用 `setMeasuredDimension()` 保存相关值。

------

什么是布局/绘制模型？可以看到协议中并没有指定该如何处理如下这些问题：

- 是允许0个，1个还是多个子节点？ （子节点模型 children model)
- 使用什么坐标系，笛卡尔坐标系还是极坐标系或是别的？

这些问题描述了模型。RenderBox (RenderObject 子类) 正是一个具体的模型，它要求使用笛卡尔坐标系，并提供定义了明确的子节点模型：

- 如果子节点数量为0 (例如 RenderImage, RenderParagraph, RenderErrorBox)，不必处理
- 如果子节点数量为1 (例如 RenderOpacity)，通常继承 RenderProxyBox 即可
- …

## RenderObject 的方法

或许你已经发现 RenderObject 更像 Android View 的对应物。的确如此，两者不仅从概念上类似，很多方法也几乎一一对应。

|                   | Flutter RenderObject                                         | Android View                                     |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| 绘制              | **paint()**                                                  | draw()/onDraw()                                  |
| 布局              | **performLayout()**/layout()                                 | measure()/onMeasure(), layout()/onLayout()       |
| 布局约束          | Constraints                                                  | MeasureSpec                                      |
| 布局协议1         | performLayout() 的 Constraints 参数表示父节点对子节点的布局限制 | measure() 的两个参数表示父节点对子节点的布局限制 |
| 布局协议2         | performLayout() 应调用各子节点的 layout()                    | onLayout() 应调用各子节点的 layout()             |
| 布局参数          | parentData                                                   | mLayoutParams                                    |
| 请求布局          | markNeedsLayout()                                            | requestLayout()                                  |
| 请求绘制          | markNeedsPaint()                                             | invalidate()                                     |
| 添加 child        | adoptChild()                                                 | addView()                                        |
| 移除 child        | dropChild()                                                  | removeView()                                     |
| 关联到窗口/树     | attach()                                                     | onAttachedToWindow()                             |
| 从窗口/树取消关联 | detach()                                                     | onDetachedFromWindow()                           |
| 获取 parent       | parent                                                       | getParent()                                      |
| 触摸事件          | hitTest()                                                    | onTouch()                                        |
| 用户输入事件      | handleEvent()                                                | onKey()                                          |
| 旋转事件          | rotate()                                                     | onConfigurationChanged()                         |

此外，一些方法调用规则也类似。比如：

> The setter will typically finish with either a call to markNeedsLayout, if the layout uses this property, or markNeedsPaint, if only the painter function does. (No need to call both, markNeedsLayout implies markNeedsPaint.)

- setter() 方法最后通常会调用 `markNeedsLayout()` 或 `markNeedsPaint()`
- 调用 `markNeedsLayout()` 意味着也自动调用了 `markNeedsPaint()`

以这段 Android 代码为例，`ColorDrawable.setColor()` 方法修改颜色主动调用 `invalidateSelf()`，最终会调用到某个 View 的 `invalidate()` 导致重新绘制。

```
public class ColorDrawable extends Drawable {

    public void setColor(@ColorInt int color) {
        if (mColorState.mBaseColor != color || mColorState.mUseColor != color) {
            mColorState.mBaseColor = mColorState.mUseColor = color;
            invalidateSelf();
        }
    }
}
```

RenderObject 的方法串起来大致是这样：

![RenderObject lifecycle -w600](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15774456110882.jpg)

# RenderBox 示例

最后通过一个示例来加深前面提到一些知识点。

我实现了自定义的 `CircleTextWidget`，直接将其展示出来：

```
void main() {
  runApp(Center(
      child: SizedBox(
          width: 200,
          height: 200,
          child: Center(
            child: CircleTextWidget(
              child: Text(
                'hello, renderBox',
                textDirection: TextDirection.ltr,
              ),
            ),
          ))));
}
```

Widget 树和渲染树如下图：

![Widget树和渲染树 -w800](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15774366867668.jpg)

CircleTextWidget 运行起来的效果图如下，

![CircleTextWidget -w800](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15774370864830.jpg)

再来看 CircleTextWidget 的代码：

```
class CircleTextWidget extends SingleChildRenderObjectWidget {
  CircleTextWidget({
    Key key,
    Widget child,
  }) : super(key: key, child: child);

  @override
  RenderCircleText createRenderObject(BuildContext context) =>
      RenderCircleText();
}

class RenderCircleText extends RenderProxyBox {
  RenderCircleText({RenderBox child}) : super(child);

  @override
  void paint(PaintingContext context, Offset offset) {
    super.paint(context, offset);

    // 父节点的宽高分别是 200, 200，由前面的 SizedBox 指定
    // dx, dy 分别是 100, 100
    double dx = constraints.constrainWidth() / 2;
    double dy = constraints.constrainHeight() / 2;

    // 中心点坐标
    Offset center = offset.translate(size.width / 2, size.height / 2);
    // 左上角坐标
    Offset topLeft = center.translate(-dx, -dy);
    // 右上角坐标
    Offset topRight = center.translate(dx, -dy);
    // 左下角坐标
    Offset bottomLeft = center.translate(-dx, dy);
    // 右下角坐标
    Offset bottomRight = center.translate(dx, dy);

    Paint paint = Paint()..color = Colors.amber;

    // 绘制蓝色矩形中心点
    context.canvas.drawRect(
        Rect.fromCenter(center: center, width: 18, height: 18),
        paint..color = Colors.blue);

    // 绘制四角的圆形点
    context.canvas.drawCircle(topLeft, 10, paint..color = Colors.red);
    context.canvas.drawCircle(topRight, 10, paint..color = Colors.yellow);
    context.canvas.drawCircle(bottomLeft, 10, paint..color = Colors.orange);
    context.canvas.drawCircle(bottomRight, 10, paint..color = Colors.green);

    // 绘制子节点
    if (child != null) {
      context.paintChild(child, offset);
    }
  }
}
```

## RenderCircleText 类

RenderCircleText 继承自 RenderProxyBox。RenderObject 定义了布局和绘制协议，但没有实现具体的布局模型。RenderBox 是 RenderObject 子类，实现了 box 布局模型。RenderProxyBox 是 RenderBox 的子类，它特别适合这种情形：父节点几乎跟子节点完全一样。

我们这里的 RenderCircleText 其实只是为其子节点绘制了自定义背景，所以继承自 RenderProxyBox 是个不错的选择。

## paint() 方法

RenderCircleText 实现了 [paint()](https://api.flutter.dev/flutter/rendering/RenderObject/paint.html) 方法。它的原型如下：

```
void paint(PaintingContext context, Offset offset)
```

`paint()` 方法涉及到的几个要点：

- `PaintingContext` 参数 - PaintingContext 是一个高级画布，它的绘制对象是各种 RenderBox。PaintingContext 还持有一个 Canvas 对象(低级画布)，它的绘制对象各种形状、path、图像、文本等元素
- `Offset` 参数 - Offset 表示当前 RenderObject 的绘制起点(屏幕左上角是原点 Offset(0,0))。在本例当中这个绘制起点为 Offset(130.0, 350.3)
- `constraints` 成员 - 父节点给当前 RenderBox 设置的约束，见下图
- `size` 成员 - 当前 RenderBox 的大小，见下图

![paint()方法 -w800](https://blog-1251688504.cos.ap-shanghai.myqcloud.com/2019/12/27/15774403469638.jpg)

结合 `constraints` 和 `offset` 我们不难计算出这几个点的坐标：

- 中心点
- 左上角
- 右上角
- 左下角
- 右下角

先调用 Canvas 的方法给子节点绘制出背景：

- canvas.drawRect()
- canvas.drawCircle()

再调用 PaintingContext 的方法绘制出子节点：

- context.paintChild()

# 总结

本文先从 Widget 是否具备自定义绘制能力出发，引出 RenderObject，然后梳理了 Widget、Element 和 RenderObject 三者之间的关系。接下来通过与 Android View 类比的方式介绍了 RenderObject。

理解 RenderObject 的几个关键在于：

- RenderObject 类层级是渲染库的核心
- RenderObject 定义布局和绘制协议
- RenderObject 是 Android View 的对应物
- RenderBox 实现一种布局和绘制模型

最后的示例中实现了一个可自定义绘制的 Widget，以加深对 RenderObject 的理解。

# 更新

2020-04-26 Update

补充一张 render tree 生命周期相关的图

![img](https://www.sunmoonblog.com/images/15878833456674.jpg)

# 参考

- [Flutter rendering](https://github.com/flutter/website/blob/master/src/docs/resources/rendering.md)
- [Flutter, what are Widgets, RenderObjects and Elements?](https://medium.com/flutter-community/flutter-what-are-widgets-renderobjects-and-elements-630a57d05208)
- [Flutter框架分析（一）– 总览和Window - 掘金](https://juejin.im/post/5c7cd2f4e51d4537b05b0974)