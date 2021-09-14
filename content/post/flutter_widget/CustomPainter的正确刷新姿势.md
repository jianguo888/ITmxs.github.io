---
title: "CustomPainter的正确刷新姿势"
date: 2021-09-14T08:59:23+08:00
draft: true
---

#### 二、动画中画板的刷新

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/89b04d200f334c2680292b8bc1e60d55~tplv-k3u1fbpfcp-watermark.awebp)

##### 1. 较高层状态类使用的 `setState` (不推荐)

通过 [ValueListenableBuilder](https://juejin.cn/post/6912206134078078990) 篇，我们应该知道在较上级的 `State` 类中执行 `setState` 会导致更多的 `Build` 过程。如下代码中通过监听 `AnimationController` ，并 `setState` 对当前 `build` 方法下的节点进行更新，从而实现颜色的变化。

```dart
class _HomePageState extends State<HomePage> with SingleTickerProviderStateMixin {
  AnimationController _ctrl;
  @override
  void initState() {
    super.initState();
    _ctrl = AnimationController(vsync: this, duration: Duration(seconds: 3))
      ..addListener(_update);
    _ctrl.forward();
  }
  
  @override
  void dispose() {
    _ctrl.dispose();
    super.dispose();
  }
  
  void _update() {
    setState(() {});
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: Padding(
        padding: EdgeInsets.all(20),
        child: CustomPaint(
          size: Size(100, 100),
          painter: ShapePainter(
              color: Color.lerp(Colors.red, Colors.blue, _ctrl.value)),
        ),
      ),
    );
  }
}
复制代码
```

------

##### 2.退而求其次的`局部刷新` (不推荐)

那也许你会说，只要降低刷新的节点，将 `画板组件` 单独`抽离`出去，或使用 [ValueListenableBuilder](https://juejin.cn/post/6912206134078078990) 局部刷新不就好了吗？如果看了  [ValueListenableBuilder](https://juejin.cn/post/6912206134078078990) 的源码就会发现，其实它的本质就是 `组件抽离`，只不过对其进行封装，回调出 `builder` 简化用户使用。如下是使用 ValueListenableBuilder 局部构建的组件，这样可以不使用 `setState` 实现组件的重建，我还是想要着重强调一句：并不是说 `setState` 不好，而是看它重建的范围，[ValueListenableBuilder](https://juejin.cn/post/6912206134078078990) 源码中也是基于 `State#setState` 进行重构的，并不是一个东西`非好即坏`，还需要看使用的场景和时机。

```dart
---->[_HomePageState#build]----
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(),
    body:  ValueListenableBuilder(
        valueListenable: _ctrl,
        builder:(ctx,value,child) => CustomPaint(
          size: Size(100, 100),
          painter: ShapePainter(color: Color.lerp(Colors.red, Colors.blue, value)),
        ),
      ),
  );
}
复制代码
```

也许你会觉得，现在不是很好吗，现在重建只是对于 `CustomPaint` 而言了，已经控制了重建的粒度。但重要的一点是 `CustomPaint` 被重建了，`ShapePainter` 也会随之重建，如下的调试，是动画过程中两次 paint 时情况。通过下面的 `this` 可以看出，当前对象的内存地址是不一样，说明每次更新画板都是不同的。这对于动画来说是灾难性的，每 16 ms 都会构建一次画板，这样的频率，即使是局部刷新，也不是最佳选择。那有没有一种方式，可以`悄无声息`的地进行绘制，而不会触发任何组件的重构？答案是 `有的!`。

| 第一次                                                       | 第二次                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d73e7dfa5e584851bd0456c744abead6~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65c8a62fd3004e949966a5943a2a71ad~tplv-k3u1fbpfcp-watermark.awebp) |

------

##### 3.画板基于监听器的重绘 (推荐)

在刚才 `ValueListenableBuilder` 版的基础上稍作修改，我们就可以完成这个需求。首先，剔除掉 `ValueListenableBuilder`，然后将 `Animation<double>` 作为 `ShapePainter` 的成员 factor，在构造函数中传入。并使用 `super(repaint: factor)` 为成员 `repaint` 赋值。`repaint` 是 CustomPainter 的成员，类型为 `Listenable` 可监听对象，当 `repaint` 值变化时，会通知画板进行 paint 重绘。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/143cef1e7b1647b88835d3abd6d110cb~tplv-k3u1fbpfcp-watermark.awebp)

```dart
---->[_HomePageState#build]----
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(),
    body: CustomPaint(
      size: Size(100, 100),
      painter: ShapePainter(factor: _ctrl),
    ),
  );
}

class ShapePainter extends CustomPainter {
  final Animation<double> factor;
  ShapePainter({this.factor}) : super(repaint: factor);
  @override
  void paint(Canvas canvas, Size size) {
    Paint paint = Paint()
      ..color = Color.lerp(Colors.red, Colors.blue, factor.value);
    canvas.drawCircle(
        Offset(size.width / 2, size.height / 2), size.width / 2, paint);
  }
  @override
  bool shouldRepaint(covariant ShapePainter oldDelegate) {
    return oldDelegate.factor != factor;
  }
}
复制代码
```

通过这种方式，点击时在 `paint` 方法断点调试，结果如下。可以看出，在完成颜色变化的同时，没有任何组件的重建，`ShapePainter` 对象也没有变化，是不是感觉非常神奇。

| 第一次                                                       | 第二次                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/57d01cd684b140fc81f78cd1def378ba~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62c7675c8a094ff7a6626b5d483680c1~tplv-k3u1fbpfcp-watermark.awebp) |

------

也许有人会问，这些你是怎么知道的？当一个疑问一直萦绕心头时，我就会想办法去研究它，而研究它最好的途径就是`不断测试`和`分析源码`。目标可以是 `CustomPainter` 的源码本身，也可以是源码中使用到`CustomPainter`的地方。 其实很多知识，一直都写在源码中，只是很少人看到。通过  `CustomPainter` 的注释可以发现，触发重绘最高效的方式都是基于`可监听对象` 实现的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fb4f0f8a94574b4589b2770b9b7305c1~tplv-k3u1fbpfcp-watermark.awebp)

```dart
触发重绘的最高效方式是:
[1]：继承 [CustomPainter] 类，并在构造函数提供一个 'repaint' 参数，
     当需要重新绘制时，该对象会进行通知它的监听者。
[2]：继承 [Listenable] (比如通过 [ChangeNotifier])并实现 [CustomPainter]，
			这样对象本身就可以直接提供通知。
复制代码
```

------

#### 三、`CustomPainter` 在 Flutter 框架中的应用

其实 CustomPainter 在  Flutter 框架源码中的应用并不是非常多，一共也就下面的 20 处。这些都是源码中对 `CustomPainter` 的使用，就表示这些使用的方式相对而言是 `最正规` 的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ff63e3bd9364edfa29d9d24c9918623~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 1. `_CupertinoActivityIndicatorPainter`

第一次的 `悟道` ，是在 `_CupertinoActivityIndicatorPainter` 源码中，也就是那个 `iOS` 的菊花转的绘制画板。 position 是一个 `Animation<double> ` 类型的对象，`Animation` 也是一个 `Listenable` 。当时发现 `CupertinoActivityIndicator` 中没有使用 `setState` 却可以触发界面的刷新，我是非常惊喜的，经过分析和研究它的实现方式，我终于发现了 `CustomPainter` 中 `repaint` 秘密。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/29f28e8391b6488e9278fb3a97629e8b~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 2. ScrollbarPainter

上面说的第二种是通过继承自 `Listenable` 并实现 `CustomPainter` 的方式，如源码中的 `ScrollbarPainter`。它是用来绘制 `ScrollBar` 组件的，通过这种方式可以让 `ScrollbarPainter` 即处理绘制，又处理通知。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/79993f25d7e747198d0022b81504bfd3~tplv-k3u1fbpfcp-watermark.awebp)

这样，在 `_CupertinoScrollbarState` 中就可以将 `ScrollbarPainter` 作为成员变量，和 State 拥有同样的生命长度。并在某些恰当的时刻，使用该对象触发相应方法进行画布重绘。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7068f8072a094e1384f893d42778e368~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 3.`_GlowingOverscrollIndicatorPainter`

当时还有一个疑惑是，`repaint` 中只是传入一个 `Listenable` 对象，那么多个属性如何去监听呢，比如多个动画同时执行。于是看到 `_GlowingOverscrollIndicatorPainter` 时便豁然开朗。它画的是滑动到顶底光晕的那个东西。 其中传入的 `leadingController`、`trailingController` 两个可监听对象。除此之外，额外传入 `repaint`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f70607b066644d34b55fda89a86e442a~tplv-k3u1fbpfcp-watermark.awebp)

可以通过 `Listenable.merge` 将多个可监听对象融合。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/86381f05415d4847b7e0c4340ad485a8~tplv-k3u1fbpfcp-watermark.awebp)

------

#### 4. `_PlaceholderPainter`

但当我觉得 `repaint` 无敌之时，仍会发现，源码中有很多绘制的类并没使用 `repaint`，而是向外界暴露属性进行设置。比如 `_PlaceholderPainter` 的矩形×，`_GridPaperPainter` 的网格，于是陷入沉思。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2ae0f5450853448a93d3e2e975b0e85d~tplv-k3u1fbpfcp-watermark.awebp)

_GridPaperPainter 的源码，只是向外界暴露绘制相关属性。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9780ef21c7364d6eb8ac8f03ee2c3d6e~tplv-k3u1fbpfcp-watermark.awebp)

最终发现了一个共性：`当绘制中含有动画和滑动处理时，都会使用 repaint 设置监听对象来触发刷新`，对于仅是静态的绘制，则使用时将绘制属性暴露出去，交由外界处理，需要刷新的话，只能通过重建画板对象。其实这也很容易理解： `动画` 和 `滑动` 的触发频率非常高，所以才会用特殊的方式进行重绘。

那么画板的重绘必须只是通过 `可监听对象` 吗？并非如此，虽然可以通过可监听对象来触发画布刷新，比如`_PlaceholderPainter` 中 color 成员变为 `ValueNotifier<Color>` ，但这样就会增加用户使用的复杂性。对于非频繁刷新的场景，局部刷新也就够了，这应该就是源码中，在非 `动画和滑动` 中不使用 `repaint` 的原因。但对于频繁触发的绘制，如 `动画` 和 `滑动`  一定要用。

最后想说一句：任何东西都不会完美无缺。成年人的世界，没有对错，只有适合与不适合。在一切的`困惑、质疑、反驳`之前，你应做的是 `多测、多想、多看`。本文就到这里，应该算是说清楚了 `CustomPainter` 正确的刷新姿势，但这也仅是 `绘制探索` 的冰山一角，`CustomPainter` 与 `CustomPaint` 背后还有很多值得探寻的东西，会随着之后的探索，为你展开一个更加丰满的 Flutter 世界。


作者：张风捷特烈
链接：https://juejin.cn/post/6916297631366905864
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
