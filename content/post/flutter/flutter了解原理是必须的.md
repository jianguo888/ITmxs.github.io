# Flutter，什么是 Widgets、RenderObjects 和 Elements？

有没有想过 Flutter 如何获取这些小部件并将它们实际转换为屏幕上的像素？不？

你应该！

了解底层技术的工作原理是优秀开发人员和优秀开发人员之间的区别。

当您知道哪些有效，哪些无效时，您可以更轻松地创建自定义布局和特殊效果；知道这一点可以让您在键盘上度过几个漫长的夜晚。

这篇文章的目的是向您介绍 flutter 表面之外的世界。我们将看看 Flutter 的不同方面，并了解它的*实际*工作原理。

# 让我们开始吧

您可能已经知道如何使用 StatelessWidget 和 StatefulWidget。但是这些小部件只组成其他小部件。布置小部件并渲染它们发生在其他地方。

**我强烈建议打开你最喜欢的 IDE 并跟随，查看实际代码中的结构通常会产生那些“啊哈”时刻。在 Intellij 中，您可以双击 shift 并输入类名来查找它。**

## 不透明度

为了熟悉 flutter 工作原理的基本概念，我们将查看[Opacity](https://github.com/flutter/flutter/blob/f38743593d00f13b21f49d8d8ee2d2206bf820d3/packages/flutter/lib/src/widgets/basic.dart#L150)小部件并检查它。因为它是一个非常基本的小部件，所以它是一个很好的例子。

它只接受一个孩子。因此，您可以将任何小部件包装在 中`Opacity`并更改其显示方式。除了孩子之外，只有一个参数被调用`opacity`，它是一个介于 0.0 和 1.0 之间的值。它控制不透明度（废话）。

# The Widget

这`Opacity`是一个`SingleChildRenderObjectWidget`.

**扩展类扩展的层次结构是这样的：**

*Opacity → SingleChildRenderObjectWidget → RenderObjectWidget → Widget*

**相比之下，StatelessWidget 和 StatefulWidget 如下所示：**

*StatelessWidget/StatefulWidget → Widget*

不同之处在于 Stateless/StatefulWidget 只*组成*小部件，而 Opacity 小部件实际上改变了小部件的绘制方式。

但是，如果您查看这些类中的任何一个，您将找不到与实际绘制不透明度相关的任何代码。

那是因为小部件只保存配置信息。 在这种情况下，不透明度小部件仅保存不透明度值。

> 这就是每次调用构建函数时都可以创建新小部件的原因。因为小部件的构建成本并不高。它们只是信息的容器。

# 渲染

但是，在**没有**渲染发生的呢？

**它在 RenderObjects 中**

从名称中您可能已经猜到，RenderObject 负责一些事情，包括渲染。

Opacity 小部件使用这些方法创建和更新 RenderObject。

```
@override
RenderOpacity createRenderObject(BuildContext context) => new RenderOpacity(opacity: opacity);

@override
void updateRenderObject(BuildContext context, RenderOpacity renderObject) {
  renderObject.opacity = opacity;
}
```

[*source*](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/basic.dart#L188)

## 渲染不透明度

该`Opacity`部件尺寸本身是完全相同的大小作为其子一样。It basically mimics every aspect of the child **but** the painting. 在绘制它的孩子之前，它会为其添加不透明度。

在这种情况下，RenderOpacity 需要实现所有方法 (例如执行布局/ hit testing/ 计算大小) 并要求其子项执行实际工作.

The `RenderOpacity` extends the `RenderProxyBox` (which mixes in a few other classes). Those are exactly implementing those methods and deferring the actual calculation to the only child.

## 

它基本上模仿了孩子的每一个方面**，但**画。

（例如执行布局/命中测试/计算大小）。

该`RenderOpacity`扩展了`RenderProxyBox`（在几个其他类融合）。那些正是实施这些方法并将实际计算推迟到唯一的孩子。

```
double get opacity => _opacity;
double _opacity;
set opacity(double value) {
  _opacity = value;
  markNeedsPaint();
}
```

**我在这段代码中删除了很多断言/优化。查看完整方法的原文** [*source*](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/rendering/proxy_box.dart#L736)

字段通常向私有变量公开一个 getter。还有一个 setter，它除了设置字段外，还调用`markNeedsPaint()`or `markNeedsLayout()`*。*顾名思义，它告诉系统“嘿我已经改变了，请重新绘制/重新布局我”。

在里面`RenderOpacity`我们找到了这个方法：

Inside the `RenderOpacity` we find this method:

```
@override
void paint(PaintingContext context, Offset offset) {
    context.pushOpacity(offset, _alpha, super.paint);
}
```

**同样，删除了优化和断言*.* [*source*](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/rendering/proxy_box.dart#L757)

该`PaintingContext`基本上是一个奇特的画布。在这个漂亮的画布上有一个叫做 pushOpacity 的方法

The `PaintingContext` is basically a fancy canvas. And on this fancy canvas there is a method called pushOpacity. *BINGO*.

这一行是*实际的*不透明度实现。

## 回顾一下

- 该`Opacity`不是`StatelessWidget`还是`StatefulWidget`而是一个`SingleChildRenderObjectWidget`。
- 将`Widget`仅持有该渲染器可以使用的信息。
- 在这种情况下，`Opacity`持有一个代表不透明度的双精度值。
- 的`RenderOpacity`，它扩展了`RenderProxyBox`执行实际布点/渲染等。
- 因为 opacity 的行为与其子级几乎完全一样，所以它将每个方法调用委托给子级。
- 它覆盖了paint 方法并调用*pushOpacity*，这将向小部件添加所需的不透明度。

## To recap

- The `Opacity` is not a `StatelessWidget` or a `StatefulWidget` but instead a `SingleChildRenderObjectWidget`.
- The `Widget` only holds information which the renderer can use.
- In this case the `Opacity` is holding a double representing the opacity.
- The `RenderOpacity`, which extends the `RenderProxyBox` does the actual layouting/ rendering etc.
- Because the opacity behaves pretty much exactly as its child it delegates every method call to the child.
- It overrides the paint method and calls *pushOpacity* which adds the desired opacity to the widget.

# That’s it? Kind of.

请记住，小部件只是一个配置，`RenderObject`唯一管理布局/渲染等。

在 Flutter 中，您基本上一直在重新创建小部件。当您的`build()`方法被调用时，您将创建一堆小部件。每次发生变化时都会调用此构建方法。例如，当一个动画发生时，build 方法会经常被调用。这意味着您不能每次都重建整个子树。相反，您想要更新它。

> 您无法获得小部件在屏幕上的大小或位置，因为小部件就像蓝图，实际上并不在屏幕上。它只是对底层渲染对象应该使用哪些变量的描述。

**元素介绍**

元素是大树中的具体小部件。

**基本上发生的事情是：**

第一次创建小部件时，它会膨胀为`Element`. 然后元素被插入到树中。如果小部件稍后发生更改，则会将其与旧小部件进行比较，并且元素会相应地更新。重要的是，元素不会被重建，它只会被更新。

元素是核心框架的核心部分，显然它们还有更多内容，但现在这些信息已经足够了。

Remember, the widget is only a configuration and the `RenderObject` only manages layout/rendering etc.

In Flutter you recreate widgets basically all the time. When your `build()` methods gets called you create a bunch of widgets. This build method is called every time something changes. When an animation happens for example, the build method gets called very often. This means you can’t rebuild the whole sub tree every time. Instead you want to update it.

> You can’t get the size or location on the screen of a widget, because a widget is like a blueprint, it’s not actually on the screen. It’s only a description of what variables the underlying render object should use.

**Introducing the Element**

The element is a concrete widget in the big tree.

**Basically what happens is:**

The first time when a widget is created, it is inflated to an `Element`. The element then gets inserted it into the tree. If the widget later changes, it is compared to the old widget and the element updates accordingly. The important thing is, the element doesn’t get rebuilt, it only gets updated.

Elements are a central part of the core framework and there is obviously more to them, but for now this is enough information.

## 不透明度示例中创建的元素在哪里？

*对于那些好奇的人来说，只是一小段。*

在`SingleChildRenderObjectWidget`创建它。

```
@override
SingleChildRenderObjectElement createElement() => new SingleChildRenderObjectElement(this);
```

[*source*](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L1631)

And the `SingleChildRenderObjectElement` is just an Element which has single child.

## The element creates the RenderObject, but in our case the Opacity widget creates its own RenderObject?

This is just for a smooth API. Because more often then not, the widget needs a `RenderObject` but no custom `Element`. The `RenderObject` is actually created by the `Element`, let’s take a look:

```
SingleChildRenderObjectElement(SingleChildRenderObjectWidget widget) : super(widget);
```

[*source*](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L4643)



而这`SingleChildRenderObjectElement`只是一个只有一个孩子的元素。

## 该元素创建了 RenderObject，但在我们的例子中，Opacity 小部件创建了它自己的 RenderObject？

这只是为了流畅的 API。因为更多时候不是，小部件需要一个`RenderObject`但没有自定义`Element`. 该`RenderObject`实际上是由创造`Element`，让我们一起来看看：



将`SingleChildRenderObjectElement`获取到的参考`RenderObjectWidget`（其中有创建的方法`RenderObject`）。

mount 方法是将元素插入元素树的地方，这里发生了神奇的事情（RenderObjectElement 类）：

The `SingleChildRenderObjectElement` gets a reference to the `RenderObjectWidget` (which has the methods to create a `RenderObject`).

The mount method is the place where the element gets inserted into the element tree, and here the magic happens (RenderObjectElement class):

```
@override
void mount(Element parent, dynamic newSlot) {
  super.mount(parent, newSlot);
  _renderObject = widget.createRenderObject(this);
  attachRenderObject(newSlot);
  _dirty = false;
}
```

*(Right after super.mount(parent,newSlot);* [*source*](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L4287)

只有一次（当它被安装时）它会询问小部件“请给我你想要使用的渲染对象，以便我可以保存它”。

# The End

And that’s it. This is how the opacity widget works internally.

My goal with this post was to introduce you to the world beyond widgets. There are still a lot of topics to cover, but I hope I could give you a nice introduction to the inner workings.

Big thanks to [Simon Lightfoot](https://twitter.com/devangelslondon?lang=en) for helping me out researching this!