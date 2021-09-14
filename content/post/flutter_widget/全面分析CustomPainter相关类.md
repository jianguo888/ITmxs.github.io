---
title: "全面分析CustomPainter相关类"
date: 2021-09-14T09:32:26+08:00
draft: true
---

##### .本文测试案例

Flutter 框架中的三位主角团 `Element`、`RenderObject`、`Widget` 是最顶层的抽象。它们三者之间存在着什么关系呢？`CustomPainter` 在这三位的光环下，又扮演这什么样的角色呢？本文将通过一个精简的绘制案例，来稍稍揭开一点  Flutter 框架运转的秘密。如下代码，直接使用 `CustomPaint` 组件作为 `runApp` 的入参。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/18f43c47b32b48ec932f06ad4861109d~tplv-k3u1fbpfcp-watermark.awebp)

```dart
void main() => runApp(CustomPaint(
      painter: ShapePainter(color: Colors.blue),
    ));

class ShapePainter extends CustomPainter {
  final Color color;
  ShapePainter({this.color});
  
  @override
  void paint(Canvas canvas, Size size) {
    Paint paint = Paint()..color = color;
    canvas.drawCircle(Offset(100, 100), 50, paint);
  }
  
  @override
  bool shouldRepaint(covariant ShapePainter oldDelegate) {
    return oldDelegate.color != color;
  }
}
复制代码
```



#### 一、CustomPainter 相关对象

##### 1. CustomPaint 类简介

现在台面上只有 `CustomPaint` 和 `CustomPainter` 两个类，下面是它们的继承关系。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a53a26a9d5754fbc99794c24127324fa~tplv-k3u1fbpfcp-watermark.awebp)

------

`CustomPaint` 是一个 Widget 的子类，我们知道 `Widget` 是一个功能很少的抽象类，主要用于 Element 的创建。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/02cf51eb1870498ba6fd898d354b9e7d~tplv-k3u1fbpfcp-watermark.awebp)

------

但衍生到 `RenderObjectWidget` 时，增加了抽象方法 `createRenderObject` 来创建 `RenderObject`。 所以这一族的 Widget 承担着创建 `RenderObject` 的使命。 `RenderObjectWidget` 本身也是抽象类，所以这两个抽象方法将由它的子类实现。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6180ad0316654bbea0df64089927b794~tplv-k3u1fbpfcp-watermark.awebp)

------

`SingleChildRenderObjectWidget` 也是抽象类，继承自 `RenderObjectWidget`，它自身通过 `SingleChildRenderObjectElement` 完成了 `createElement` 方法。所以其子类的任务就是实现创建 `RenderObject` 的那个抽象方法 `createRenderObject`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/95b981d300a84d0da23d129dec04a763~tplv-k3u1fbpfcp-watermark.awebp)

------

就这样，使命的 `星火相承`，在 `CustomPaint` 中对 `createRenderObject` 进行实现。返回的是 `RenderCustomPaint` ，这样 `RenderCustomPaint` 便登上了台面 。 `CustomPaint` 作为一个 Widget 的实现类，拥有 5 个 final 的成员属性，这些属性会在构造方法中进行初始化。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bfc8fe34cde646898ef4bb7df1fcc8e6~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 2.RenderCustomPaint 类简介

现在 `RenderCustomPaint` 出场，简图如下： ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e431793f4e9741dfabe97bf3d91214f4~tplv-k3u1fbpfcp-watermark.awebp)

------

`RenderCustomPaint` 是 `RenderObject` 的子类。RenderObject 是一个非常复杂的类，本文并不打算详细说它，后面有机会单独写一篇。这里简单地认识一下它：首先它是一个树状结构，通过 `adoptChild` 和 `dropChild` 来维持渲染树的节点。其次，该类型对象需要负责布局 `layout` 和 绘制 `paint` ，通过 `markNeedsPaint` 方法可以标记当前 `RenderObject` 需要被绘制。

```dart
abstract class RenderObject extends AbstractNode with DiagnosticableTreeMixin implements HitTestTarget {
  
  Constraints? _constraints;
  
  void layout(Constraints constraints, { bool parentUsesSize = false }) {...}
  void paint(PaintingContext context, Offset offset) { }
  
  @override
  void adoptChild(RenderObject child) { ... }
  
  @override
  void dropChild(RenderObject child) { ...  }
  
  void markNeedsPaint() {}
}
复制代码
```

------

`RenderBox` 是目前 Flutter 框架中 `RenderObject` 的直系子类。它是 `二维笛卡尔坐标系中的渲染对象`，在 `RenderObject` 中只定义了约束 `BoxConstraints` ，在 `RenderBox` 中进一步定义了尺寸 `Size` 。一个 Size 对应一个布局范围的 box ，在这个范围内就是一个左上角为 `0,0` 的笛卡尔坐标系。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b1e1eac326f04de19f41ed884c54799a~tplv-k3u1fbpfcp-watermark.awebp)

------

`RenderCustomPaint` 是最终的实现类，它实例化的时机是 `CustomPaint` 组件调用 `createRenderObject` 时。其中构造函数的入参也就是 `CustomPaint` 的那几个成员，也就是说，我们自定义的 `CustomPainter` 画板最终会交给这个类进行使用。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e72ef654868465e95989300a01e39fe~tplv-k3u1fbpfcp-watermark.awebp)

目前这几个类之间的关系简图如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1abc6e90156436ca2c502bdb56b5784~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 3.CustomPainter 类简介

`CustomPainter` 是一个抽象类，它继承自 `Listenable` ，内部还持有一个 `Listenable` 类型的成员 `_repaint` ，它有两个抽象方法 `paint` 和 `shouldRepaint` 。 所以想使用 `CustomPainter`  就需要继承它，并实现其抽象方法。它被 `CustomPaint` 持有，并传递到 `RenderCustomPaint` 类中。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b3c9220cf6384cb78dce5e1c8e7d5ed0~tplv-k3u1fbpfcp-watermark.awebp)

上一篇 [CustomPainter 正确刷新姿势 ](https://juejin.cn/post/6916297631366905864) 中说到，可以通过 `repaint` 设置可监听对象来触发画板刷新。从这里就可以看到其中的原理。在 `RenderCustomPaint#attach` 方法中 `_painter` 会添加监听执行 `markNeedsPaint` 方法，从而触发重绘。而 `CustomPainter#addListener` 就是在监听 `_repaint` 成员的变化，触发回调。

```dart
---->[RenderCustomPaint#attach]----
@override
void attach(PipelineOwner owner) {
  super.attach(owner);
  _painter?.addListener(markNeedsPaint);
  _foregroundPainter?.addListener(markNeedsPaint);
}

---->[CustomPainter#addListener]----
@override
void addListener(VoidCallback listener) => _repaint?.addListener(listener);
复制代码
```

------

#### 二、 RenderObjectElement 登场

这时会有一个问题：`CustomPaint#createRenderObject` 是在何时被触发的？遇事不决，调试一下。可以看出是在 `RenderObjectElement.mount` 方法中被触发的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5cb3b2687d58488fb937186cc2526cef~tplv-k3u1fbpfcp-watermark.awebp)

------

可以看一下当前的 this 对象，是 `SingleChildRenderObjectElement` 类型，可见它持有一个 `_widget` 成员，正是之前传入的 `CustomPaint` 对象。通过`widget.createRenderObject` 为成员 `_renderObject` 赋值。另外入参是 this，也说明入参的 `BuildContext` 对象正是当前的 `SingleChildRenderObjectElement`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4866dec9a0b44ed997e2ed4e4ec95a36~tplv-k3u1fbpfcp-watermark.awebp)

------

当 `SingleChildRenderObjectElement` 浮上台面，这张简图便丰富了。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bd4426195c874ad5aed6084cc163be9e~tplv-k3u1fbpfcp-watermark.awebp)

`Element` 是元素抽象的顶层，本身持有 `Widget` 对象，会衍生出两大种类：`ComponentElement` 和 `RenderObjectElement`。其中只有 `RenderObjectElement` 才持有 `RenderObject`。也就是说，对于`RenderObjectElement` 而言，会同时持有 `Widget` 和  `RenderObject`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9053fb05d01542f9bdf6c4233befef99~tplv-k3u1fbpfcp-watermark.awebp)

总结一下，`RenderObjectElement` 的 `RenderObject` 成员会在执行 `mount` 方法执行时进行赋值，其值为使`RenderObjectWidget#createRenderObject` 返回值。到这里便可以总结一下这几个类直接的关系了。

------

#### 三、相关各类之间的关系

##### 1、`RenderObjectWidget` 和 `RenderObject` 的关系

`RenderObjectWidget` 作为一个 `Widget` 的衍生类，拥有创建 `RenderObject` 对象的使命。其子类必须实现 `createRenderObject` 抽象方法。两者之间的关系是 `创造者-RenderObjectWidget` 和 `被创造者-RenderObject` 。
 例如：对于 `CustomPaint` 组件来说，`createRenderObject` 方法负责创建 `RenderCustomPaint` 这个 `RenderObject` 对象。

```dart
---->[CustomPaint#createRenderObject]----
@override
RenderCustomPaint createRenderObject(BuildContext context) {
  return RenderCustomPaint(
    painter: painter,
    foregroundPainter: foregroundPainter,
    preferredSize: size,
    isComplex: isComplex,
    willChange: willChange,
  );
}
复制代码
```

------

##### 2. `RenderObjectElement` 和 `RenderObject` 的关系

`RenderObjectElement` 作为一个 `Element` 的衍生类，类中会同时持有 `Widget` 和 `RenderObject` 成员。两者之间的关系是 `使用者-RenderObjectElement` 和 `被使用者-RenderObject` 。且在 `RenderObjectElement#mount` 方法中， `RenderObject` 成员被初始化。
 例如：对于 `SingleChildRenderObjectElement` 对象来说，`RenderObject` 类型的 `_renderObject` 是该对象的一个成员。

```dart
---->[RenderObjectElement 源码]----
abstract class RenderObjectElement extends Element {
  RenderObjectElement(RenderObjectWidget widget) : super(widget);
  @override
  RenderObjectWidget get widget => super.widget as RenderObjectWidget;

  @override
  RenderObject get renderObject => _renderObject; 
  RenderObject _renderObject;
  @override
  
  void mount(Element? parent, dynamic newSlot) {
    super.mount(parent, newSlot);
    _renderObject = widget.createRenderObject(this); // RenderObject 成员初始化
  // ...
}
复制代码
```

------

##### 3.`Widget` 和 `Element` 的关系

从 Widget 类的那短短几行源码也可以看出：`Widget 最重要的目的是创建 Element ` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fdfb6ac05ce74ef38e8b3d2d06decfeb~tplv-k3u1fbpfcp-watermark.awebp)

每个 Element 都会持有一个 Widget 对象，并且这个 Widget 对象创造了该元素。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/950a2ae1bfe746de9bcfd6d851f28a49~tplv-k3u1fbpfcp-watermark.awebp)

另外， `Widget#createElement` 在整个 Flutter 框架中`只出现过两次`，其一是根元素节点创建时。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c21a5611f5b84cfab1ec9e311a39cff0~tplv-k3u1fbpfcp-watermark.awebp)

其二就是的 `Element#inflateWidget`，可以说这里是 `Element` 的孵化室，除了根元素节点外，所以的 Element 都是在这个方法里实例化的。这个主题有机会再细说。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc4cbb50ecbc4751ab93a8093a168427~tplv-k3u1fbpfcp-watermark.awebp)

现在应该对整体有了一个简单地把握，通过下面的图片可以让你更形象地认识它们之间的关系。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5ee71bf57a04d5c87be7ef2c799d813~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 4.CustomPainter 相关类小结

我们可以看出，和 `CustomPainter` 相关的类有 `CustomPaint` 这个 Widget，以及 `RenderCustomPaint`这个 RenderObject 对象。 `CustomPaint` 只是作为一个载体，将画板对象传给 `RenderCustomPaint` 。所以和`CustomPainter` 关系最密切的是`RenderCustomPaint` 类。在该类中会对 `CustomPainter` 中的可监听对象进行监听，触发 `RenderCustomPaint` 对象的重绘，另外 `CustomPainter` 的绘制操作也是在该类中进行回调的，这里也是 `CustomPainter#paint` 中 Canvas 对象的来源之处。

本文简单梳理了一下和绘制相关的几个类之间的关系，下一篇将进一步探索 Flutter 绘制相关的源码，全面分析`CustomPainter`  这个类，着重看一下 `paint` 和 `shouldRepaint` 两个抽象方法触发的时机。

