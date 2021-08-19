---
title: "Flutter组件Switch"
date: 2021-08-17T13:38:45+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

#### 一、 Switch 组件使用详解

可能有人会觉得 `Switch` 组件非常简单，有什么好说的呢？其实 `Switch` 组件源码洋洋洒洒 `近千行` ，其中关于`主题处理`、`平台适配`、`事件处理`、`动画处理`、`绘制处理` 都有值得我们学习的地方。那么废话不多说，来一起看看  `Switch`  是怎么炼成的吧。

##### 1. Switch 最简使用：`value` 与 `onChanged`

`Switch` 组件的使用中注意：该组件是 `StatelessWidget` ，表示本身并不维护 `开关状态`。这也就意味着，我把只能通过 `重新构建 Switch组件`  来切换 `开关状态` 。在构建 `Switch` 时必须传入 `value` 和 `onChanged` 两个参数，其中 `value` 表示 `Switch` 开关的状态，`onChanged` 是状态变化回调函数。

如下，在 `_SwitchDemoState` 中定义状态 `_value` 用于表示 `Switch` 开关的状态，在 `_onChanged` 回调中改变状态值，并 `重新构建 Switch` 组件，这样就能达到`点击进行开关`的效果。

```dart
class SwitchDemo extends StatefulWidget {
  const SwitchDemo({Key? key}) : super(key: key);

  @override
  _SwitchDemoState createState() => _SwitchDemoState();
}

class _SwitchDemoState extends State<SwitchDemo> {
  bool _value = false;

  @override
  Widget build(BuildContext context) {
    return Switch(
      value: _value,
      onChanged: _onChanged,
    );
  }

  void _onChanged(bool value) {
    setState(() {
      _value = value;
    });
  }
}
复制代码
```

其实这里可能很让人疑惑 `Switch` 为什么不自己维护  `开关状态`，要将改状态`交由外界指定`呢？既然 `Switch`  是 `StatelessWidget` ，为什么可以执行滑动的动画？还有 `onChanged` 方法又是何时触发的？带着这些问题我们来逐渐去认识这个属性而陌生的 `Switch` 组件。

------

##### 2.  Switch 的四个主要颜色

从 `Switch` 的构造方法中可以看出，其中定义了非常多的颜色相关属性。

先看前四个颜色属性：

- `inactiveThumbColor` 代表关闭时`圆圈`的颜色。
- `inactiveTrackColor` 代表关闭时`滑槽`的颜色。

- `activeColor` 代表打开时`圆圈`的颜色。
- `inactiveTrackColor` 代表打开时`滑槽`的颜色。

```dart
Switch(
  activeColor: Colors.blue,
  activeTrackColor: Colors.green,
  inactiveThumbColor: Colors.orange,
  inactiveTrackColor: Colors.pinkAccent,
  value: _value,
  onChanged: _onChanged,
);
复制代码
```

------

##### 3.  hoverColor 、 mouseCursor 和 splashRadius

前两个属性一般只能在桌面或web 端起作用，`hoverColor` 顾名思义是鼠标悬浮时，外层的大圈颜色，`splashRadius` 表示大圈的半径，如果不想要外圈的悬浮效果，可以将半径设为 0 。另外， `mouseCursor` 代表鼠标的样式，比如下面的小拳头是 `SystemMouseCursors.grabbing` 。

处。

```dart
Switch(
  activeColor: Colors.blue,
  activeTrackColor: Colors.green,
  inactiveThumbColor: Colors.orange,
  inactiveTrackColor: Colors.pinkAccent,
  hoverColor: Colors.blue.withOpacity(0.2),
  mouseCursor: SystemMouseCursors.grabbing,
  value: _value,
  onChanged: _onChanged,
);
复制代码
```

`mouseCursor` 属性的类型为 `MouseCursor` ，其中 `SystemMouseCursors` 中定义了非常多的鼠标指针类型以供使用。下面给出几个效果：

##### . 指定图片

通过 `activeThumbImage` 和 `inactiveThumbImage` 可以指定小圆中`开启/关闭` 时的图片。另外 `onActiveThumbImageError` 和 `onInactiveThumbImageError` 两个回调用于图片加载错误的监听。

当小圆同时指定 `图片`和 `颜色` 属性时，会显示  `图片` 。

```dart
Switch(
  activeColor: Colors.blue,
  activeThumbImage: AssetImage('assets/images/icon_head.png'),
  inactiveThumbImage: AssetImage('assets/images/icon_8.jpg'),
  activeTrackColor: Colors.green,
  inactiveThumbColor: Colors.orange,
  inactiveTrackColor: Colors.pinkAccent,
  hoverColor: Colors.blue.withOpacity(0.2),
  mouseCursor: SystemMouseCursors.move,
  splashRadius: 15,
  value: _value,
  onChanged: _onChanged,
);
复制代码
```

------

##### 6.主题相关属性: thumbColor 和 trackColor

一些具有交互性的 `Material` 组件会通过有 `MaterialState` 枚举定义交互行为，有如下 `7` 个元素。

```dart
enum MaterialState {
  hovered,
  focused,
  pressed,
  dragged,
  selected,
  disabled,
  error,
}
复制代码
```

可以看出这两个成员都是 `MaterialStateProperty` 类型，那这种类型的对象如何创建，又有什么特点呢？

```dart
---->[Switch 成员声明]----
final MaterialStateProperty<Color?>? thumbColor;
final MaterialStateProperty<Color?>? trackColor;
复制代码
```

------

简单来说通过 `MaterialStateProperty.resolveWith` 方法，传入一个函数返回对应泛型数据。如下回调函数为 `getThumbColor` ，回调参数为 `Set<MaterialState>` 。也仅仅说，会根据 `MaterialState` 集合，来返回泛型数据。从 `thumbColor` 属性源码注释中可以看出，`Switch` 有如下四种 `MaterialState` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6e3dd7a967cf422898bf58801d67940f~tplv-k3u1fbpfcp-watermark.awebp)

在 `getThumbColor` 中根据 `states` 的情况，分别对几种状态返回不同颜色，这样 `Switch` 在不同的状态下，就会自动使用对应颜色。比如下面的 `onChanged: null` 代表  `Switch`  不可用，在  `getThumbColor` 中当为 `disabled` ，会返回红色。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/928d8be1ac8247a5bf022126fa9f260d~tplv-k3u1fbpfcp-watermark.awebp)

`thumbColor` 代表小圆颜色，`trackColor` 代表`滑槽`颜色，使用方式是一样的。这里可能有人会问：有三个属性可以设置小圆，那它们同时存在，优先级怎么样？结果测试发现，`inactiveThumbImage` 会优先显示，优先级如下：

```dart
inactiveThumbImage > thumbColor > inactiveThumbColor > 默认 Switch 主题
复制代码
```

------

上面提到了 ` 默认 Switch 主题` ，这里就来说一下 `SwitchTheme` ，它是一个 `InheritedWidget`，维护 `SwitchThemeData` 类型数据，具体内容如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70084d3043ac4474a97af091fd2eb24f~tplv-k3u1fbpfcp-watermark.awebp)

我们可以通过在上层嵌套 `SwitchTheme` 来为子树中的 `Switch` 指定默认样式，由于 `MaterialApp` 内部继承了 `SwitchTheme `组件，我们可以在 `theme` 中指定  `Switch` 的主题样式。这样在指定  `Switch`  的相关颜色属性，就会使用默认的主题样式：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/86b72bbb3ba645a2888ff60fab23bb84~tplv-k3u1fbpfcp-watermark.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5196b4a6b4484ed9a09fc0c0124a0a40~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 7. Switch 的焦点:  focusColor 与 autofocus

`Switch` 组件是拥有焦点的，焦点相关的处理被封装在组件内部。`focusColor` 表示聚焦时的颜色，可被聚焦的组件有个特点：在桌面或 web 平台中可以通过 `Tab` 键，切换焦点。如下是六个 `Switch` 通过 `Tab` 键切换焦点的效果：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60aa02de5daf44afa18f8854c301bb1b~tplv-k3u1fbpfcp-watermark.awebp)

```dart
@override
Widget build(BuildContext context) {
  return
    Wrap(
      children: List.generate(6, (index) => Switch(
        value: _value,
        focusColor: Colors.blue.withOpacity(0.1),
        onChanged: _onChanged,
      ))
    );
}
复制代码
```

------

##### 8. Switch 的尺寸相关: materialTapTargetSize

`MaterialTapTargetSize` 是一个枚举类型，有两个元素。该属性可以影响 `Switch` 的大小，如下分布是 `padded` 和 `shrinkWrap` 的效果。通过调试可知，默认是 `padded` 。下面在源码分析中会详细介绍该属性的作用。

```dart
enum MaterialTapTargetSize {
  padded,
  shrinkWrap,
}
复制代码
```

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b6e6dcc5f8d4b5fb3c2ed865923fb83~tplv-k3u1fbpfcp-watermark.awebp)

------

#### 二、 挖掘 Switch 源码中的一些细节

##### 1. 类型 `_SwitchType`

`Switch` 类中有一个 `_SwitchType` 类型成员，该成员完全被封装在 `Switch` 内部，我们是无法直接操作的。 `_SwitchType` 是只有`两个元素`的枚举类。

```dart
enum _SwitchType { material, adaptive }

---->[Switch 成员声明]----
final _SwitchType _switchType;
复制代码
```

既然是成员变量，必然会在类内部被初始化，一般来说对 `成员变量` 初始化的地方在 `构造方法` 中。如下，` Switch 的普通构造` 中，会将 `_switchType` 设为 `_SwitchType.material` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8dc1150849f248eb98f5a928c82517e1~tplv-k3u1fbpfcp-watermark.awebp)

------

一般来说，枚举对象就是为了分类处理，在 `Switch#build` 方法中，会根据 `_switchType` 的值进行不同的`构建逻辑`，如果是 `material` ，则所有的平台都使用`Material`风格的 `Switch` 。 如果是 `adaptive` 会根据平台的不同，使用不同的风格的 `Switch` 。在 `android`、 `fuchsia`、 `linux`、 `windows` 中会使用 `Material` 风格；在 `iOS` 和 `macOS` 中会使用 `Cupertino` 风格。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d9b3f31b7f814ed8bf930ddf93341de3~tplv-k3u1fbpfcp-watermark.awebp)

到这里，可能有人会问， `_SwitchType` 成员完全被封装在 `Switch` 内部，那如何设置 `adaptive` 类型呢？仔细查看源码可以看出 `Switch` 还有一个 `adaptive` 构造，此处会将  `_switchType` 设为 `_SwitchType.adaptive` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fcb2eae01b3d4ab29b32c56cd1c23f3f~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 2. 两种风格的 Switch 构建

`_buildCupertinoSwitch` 是当模式为 `adaptive` 时，用于构建 `iOS` 和 `macOS` 平台 `Switch` 组件构建，可以看出其内部是通过 `CupertinoSwitch` 进行构建，效果如下：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/85b17096d0324dd8a63c8b498ba09fd6~tplv-k3u1fbpfcp-watermark.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/710fe459fa6b4a268f7dd7eafc1ec008~tplv-k3u1fbpfcp-watermark.awebp)

------

`_buildMaterialSwitch` 用于构建 `Material` 风格的 `Switch` 组件构建，可见其内部通过 `_MaterialSwitch` 组件进行构建。到这里我们就可以回答：既然 `Switch`  是 `StatelessWidget` ，为什么可以执行滑动的动画？因为 `_MaterialSwitch` 组件是 `StatefulWidget` ，它可以在内部改变组件状态。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69516cd90ee44eb6b9e91968a3c81eaa~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 3.Switch 尺寸的确定

从上面可以看出，两种风格的 `Switch` 都是通过 `_getSwitchSize` 获取 `Size` 尺寸的。如下代码中，可以看出，尺寸是通过 `MaterialTapTargetSize` 对象控制的。如果未指定 `materialTapTargetSize` 则会通过主题获取，调试可以看出，主题中 `materialTapTargetSize` 默认是 `padded` 。

```dart
Size _getSwitchSize(ThemeData theme) {
  final MaterialTapTargetSize effectiveMaterialTapTargetSize = materialTapTargetSize
    ?? theme.switchTheme.materialTapTargetSize
    ?? theme.materialTapTargetSize;
  switch (effectiveMaterialTapTargetSize) {
    case MaterialTapTargetSize.padded:
      return const Size(_kSwitchWidth, _kSwitchHeight);
    case MaterialTapTargetSize.shrinkWrap:
      return const Size(_kSwitchWidth, _kSwitchHeightCollapsed);
  }
}
复制代码
```

下面分别是 `padded` 和 `shrinkWrap` 的调试信息，可以很清楚地看出尺寸情况。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aefc427f9cec4f6c9800f58670071776~tplv-k3u1fbpfcp-watermark.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/131bb2e26a8a469da9071a9d3b1ce76e~tplv-k3u1fbpfcp-watermark.awebp)

------

到这里 `Switch` 组件的源码就已经面面俱到了，我们可以发现，它作为一个 `StatelessWidget` 并不能做太多的事，只是定义了很多属性，并通过别的组件进行构建。也就是说，它本身起到平台差异的统筹、封装的作用，目的就是方便用户使用。

------

##### 4.  onChanged 方法触发的时机

通过调试可以发现，`onChanged 方法` 的触发是 `ToggleableStateMixin#_handleTap` 中触发的。如下是 `buildToggleable` 的源码，可以看出其中通过 `GestureDetector` 监听点击事件。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/618c01a9edec414eab18b466af855385~tplv-k3u1fbpfcp-watermark.awebp)

在 `_MaterialSwitchState.build` 方法中，可以看到其中通过 `GestureDetector` 监听了水平拖拽事件，这也是为什么 `Switch` 可以支持拖动的原因，同时 `child` 属性是 `buildToggleable` ，也就是上面的组件，支持点击事件。这是一个很好的多事件监听的案例。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b6233fa732ba4bf7bd7c04ca06b17bf3~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 5.动画的创建与触发

仔细看一下滑动的过程，可以看出其中有 `位移动画` 和 `透明度渐变动画`。 首先来说一下动画的来源：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d821f9722c545249f7d15f8d3566e02~tplv-k3u1fbpfcp-watermark.awebp)

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210803082702625.png)

------

这些动画器都定义在 `ToggleableStateMixin` 中。而 `_MaterialSwitchState` 混入了 `ToggleableStateMixin` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/511a9c1f375441c4ab023d3521ef8bb2~tplv-k3u1fbpfcp-watermark.awebp)

------

和隐式动画一样， `_MaterialSwitchState`  中的动画触发也是通过重构组件，执行 `didUpdateWidget` 。如果你了解隐式动画，就不难理解 `Switch` 的动画触发机制。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cfceced55e2847af931066033561e942~tplv-k3u1fbpfcp-watermark.awebp)

最后，绘制是通过 `_SwitchPainter` 画出来的，这个画板是比较复杂的，这里就不展开了，有兴趣的可以自己研究一下。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81b97b0e4ffa488597e005ea158a7d4a~tplv-k3u1fbpfcp-watermark.awebp)

`Switch 组件`的使用方式到这里就完全介绍完毕，那本文到这里就结束了，谢谢观看，明天见~


作者：张风捷特烈
链接：https://juejin.cn/post/6991998231458611231
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。