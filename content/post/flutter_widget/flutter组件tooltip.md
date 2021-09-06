---
title: "Flutter组件BackdropFilter"
date: 2021-09-06T09:02:22+08:00
draft: true
---

#### 1.认识 BackdropFilter 组件

`BackdropFilter` 组件可能很少人使用，但它的功能还是很强大的。源码中对它的介绍是：对已有的绘制内容添加一个过滤器，然后再绘制它的孩子。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3ed87ebec4244319ba817c0e96d84f03~tplv-k3u1fbpfcp-watermark.awebp)

------

下面是 `BackdropFilter` 组件类的`定义`和 `构造方法`，可以看出它继承自 `SingleChildRenderObjectWidget` 。构造时必须传入尺寸 `filter` 参数，其类型是 `ImageFilter`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb4b2e65fe2c4073800ac6e5ae2e57bc~tplv-k3u1fbpfcp-watermark.awebp)

```dart
final ui.ImageFilter filter;
复制代码
```

------

#### 2.BackdropFilter 的使用

源码中有一个 `BackdropFilter` 组件的测试案例，我们先基于这个案例，看一下 `BackdropFilter ` 的效果及作用。下图中，有三个区域： `01文字`、`紫色区域`、`Hello World` 文字。实现的方式是：通过 `Stack` 叠合 `01文字` 和 `BackdropFilter` ，其中紫色区域和`Hello World` 文字是 `BackdropFilter` 的子组件。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fbd3be41fe1c4d808d6a21ae467b5518~tplv-k3u1fbpfcp-watermark.awebp)

```dart
class CustomBackdropFilter extends StatelessWidget {
  final Random random = Random();

  @override
  Widget build(BuildContext context) {
    String data = '';
    for (int i = 0; i < 10000; i++) {
      data += random.nextBool() ? " 0 " : " 1 ";
    }

    return Stack(
      children: <Widget>[
        Text(data),
        Center(child: buildFilterZone(),),
      ],
    );
  }

  Widget buildFilterZone() {
    return BackdropFilter(
      filter: ui.ImageFilter.blur(
        sigmaX: 2.0,
        sigmaY: 2.0,
      ),
      child: Container(
        alignment: Alignment.center,
        width: 200.0,
        height: 120.0,
        color: Colors.purple.withOpacity(0.1),
        child: const Text(
          'Hello World',
          style: TextStyle(fontSize: 24),
        ),
      ),
    );
  }
}
复制代码
```

从布局查看器中可以看出：`BackdropFilter` 的区域只是紫色部分，模糊遮罩并不会对`其子组件`产生影响。就像是在组件上层覆盖一个`模糊层`，而是`子组件`会在`模糊层`之上。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3db1929009cc4a5abccb81ea7c7938aa~tplv-k3u1fbpfcp-watermark.awebp)

------

有时我们可能只是对某个区域进行遮罩处理，可以通过 `ClipRRect` 等裁剪组件进行裁剪，这样`模糊层` 就不会影响之外的部分。如下是圆角矩形的裁剪效果：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1691679a3b442d09abaa6497ed8b9ec~tplv-k3u1fbpfcp-watermark.awebp)

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(20),
  child: buildFilterZone(),
),
复制代码
```

------

#### 3. 认识 ImageFilter

首先 `ImageFilter` 是一个抽象类，但它可以通过命名构造创建对象，如下有三种构造方式。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ba5c7851f4f141d8a5bd7c4a02fa2420~tplv-k3u1fbpfcp-watermark.awebp)

拿 `ImageFilter.blur` 来说，可以看到构造前面有一个 `factory` 关键字，以此让抽象类也可以创建对象。可以看出这个构造本质上是使用了 `_GaussianBlurImageFilter` ，也就是`高斯模糊`。两个入参 `sigmaX` 和 `sigmaY` 是模糊的程度。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/671ddf0b293b4ab2b2e81962ef611c1a~tplv-k3u1fbpfcp-watermark.awebp)

------

比如下面是 `x:2.0,y:2.0` 的效果：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1f3195eabd4429cbe01a8542f58cfbc~tplv-k3u1fbpfcp-watermark.awebp)

这是 `x:4.0,y:1.0` 的效果：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a4ab510326d4e12897d4818376bf7fe~tplv-k3u1fbpfcp-watermark.awebp)

这是 `x:6.0,y:6.0` 的效果：

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0abd4b2f316f43908b944c82b1974796~tplv-k3u1fbpfcp-watermark.awebp)

可见 `sigmaX` 和 `sigmaY` 分别控制 `X` 和 `Y` 方向上的模糊程度。

------

除了通过 `ImageFilter.blur` 创建 `模糊遮罩`，还可以通过 `ImageFilter.matrix` 对区域内进行矩阵变换，如下面的 `skewX`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ecf365c73734a288d6f1aeac3e1fb18~tplv-k3u1fbpfcp-watermark.awebp)

```dart
Widget buildFilterZone() {
  return BackdropFilter(
    filter:
    ui.ImageFilter.matrix(Matrix4.skewX(45/180*pi).storage),
    child: Container(
      alignment: Alignment.center,
      width: 200.0,
      height: 120.0,
      color: Colors.blueAccent.withOpacity(0.1),
      child: const Text(
        'Hello World',
        style: TextStyle(fontSize: 24),
      ),
    ),
  );
}
复制代码
```

------

#### 4. BackdropFilter 组件的源码实现

`BackdropFilter` 继承自 `SingleChildRenderObjectWidget` ，内部维护 `RenderBackdropFilter` 渲染对象来实现添加滤色器功能。

![image-20210828154123795](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc64b75ac75344c2ad7f823b6ea91d1c~tplv-k3u1fbpfcp-watermark.awebp)

------

在 `RenderBackdropFilter#paint` 中创建 `BackdropFilterLayer` 对象 `layer`，并将传入的 `filter` 设置给 `layer` 。通过 `context.pushLayer` 添加一个层，实现滤色器功能。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fdf2e462caaf42fa9d6cfd6e3cc162cb~tplv-k3u1fbpfcp-watermark.awebp)

那本文到这里就结束了，谢谢观看，明天见~


作者：张风捷特烈
链接：https://juejin.cn/post/7001394814285512717
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
