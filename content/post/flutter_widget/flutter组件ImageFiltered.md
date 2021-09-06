---
title: "Flutter组件ImageFiltered"
date: 2021-09-06T09:13:08+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

##### 1. 认识 ImageFiltered 组件

上一篇说了 [BackdropFilter](https://juejin.cn/post/7001394814285512717) 组件，它可以在子组件下方添加一个`过滤层`，所以此效果无法作用于子组件。而 `ImageFiltered` 是将 `过滤层` 放在子组件上方，也就是过滤效果可以作用于子组件上。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/68fffbd0cfc94e0a95a2a70c62be4eaf~tplv-k3u1fbpfcp-watermark.awebp)

它继承自 `SingleChildRenderObjectWidget`，必须传入 `imageFilter` 参数，类型为 `ImageFilter`。这和 `BackdropFilter` 组件中的 `imageFilter` 是一样的。

------

##### 2. ImageFiltered 组件的使用

如下是通过 `ImageFiltered` 对图片进行模糊的效果，`imageFilter` 参数的使用和  [BackdropFilter](https://juejin.cn/post/7001394814285512717) 一样，这里不再赘述。

| 1*1                                                          | 2*2                                                          | 3*3                                                          | 5*5                                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7fb528240ea4471086b5b959313b53a3~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc0633bb311b488aab68bb2975a12867~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ca68cebce7b740379c51d5a484ca4fa2~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/86cf6bcac0de4cbfac2742983ba10edd~tplv-k3u1fbpfcp-watermark.awebp) |

```dart
class ImageFilteredDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ImageFiltered(
            imageFilter: ImageFilter.blur(
              sigmaX: 2,
              sigmaY: 2,
            ),
            child: Image.asset(
                'assets/images/sabar.webp',
                fit: BoxFit.cover,
                width: 150,
                height: 150,
            ),
    );
  }
}
复制代码
```

另外 `ImageFiltered` 并不局限于 `Image` 组件。如下将 `FlutterUnit` 整体使用 `ImageFiltered` 进行处理，这样全应用都会有模糊效果。虽然这么做没有什么太大的意义，只是说明 `ImageFiltered` 可以处理任意组件。

| -                                                            | -                                                            | -                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5798902b0826435e94d31e3d9742210f~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/be26d4ca604c4737948902539dec09b2~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c792d273208b41828b77f9588613c9d3~tplv-k3u1fbpfcp-watermark.awebp) |

```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized();
  //滚动性能优化 1.22.0
  GestureBinding.instance?.resamplingEnabled = true;
  runApp(BlocWrapper(
    child: ImageFiltered(
      imageFilter: ImageFilter.blur(
        sigmaX: 2,
        sigmaY: 2,
      ),
      child: FlutterUnit(),
    ),
  ));
}
复制代码
```

------

##### 3.ImageFiltered 组件源码实现

`ImageFiltered` 继承自 `SingleChildRenderObjectWidget` ，内部维护 `_ImageFilterRenderObject` 渲染对象来实现添加滤色器功能。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d6439942d924a0fb77d091ebffa4e33~tplv-k3u1fbpfcp-watermark.awebp)

------

在 `_ImageFilterRenderObject#paint` 中创建 `ImageFilterLayer` 对象 `layer`，并将传入的 `imageFilter` 设置给 `layer` 。通过 `context.pushLayer` 添加一个层，实现滤色器功能。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eca14de126c944239714efadea1d65d0~tplv-k3u1fbpfcp-watermark.awebp)

------

#### 二、ColorFiltered 组件

##### 1.认识 ColorFiltered 组件

`ColorFiltered` 继承自 `SingleChildRenderObjectWidget`，必须传入 `colorFilter` 参数，类型为 `ImageFilter`。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e55b65902dcb4f408904ee919deb81f1~tplv-k3u1fbpfcp-watermark.awebp)

------

##### 2. ColorFiltered 组件的使用

`ColorFiltered` 有四种构造方式，`.srgbToLinearGamma` 和 `.linearToSrgbGamma` 是固定的处理效果；`.mode` 是通过颜色和混合模式进行着色处理。通过 `.matrix` 可以进行颜色的矩阵变换。这块比较复杂，不详细展开，感兴趣的可以看一下[《Flutter 绘制指南 - 妙笔生花》](https://juejin.cn/book/6844733827265331214) 的第八章，有对着色器的详细介绍。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7cc5a97185cc4273bc0538be1516809b~tplv-k3u1fbpfcp-watermark.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/df06a9244252458ebd0b7a8ea3165898~tplv-k3u1fbpfcp-watermark.awebp)

```dart
//srgbToLinearGamma
ColorFiltered(
  colorFilter: ColorFilter.srgbToLinearGamma(),
  child:  Image.asset(
    'assets/images/sabar.webp',
    fit: BoxFit.cover,
    width: 150,
    height: 150,
  ),
)
  
//linearToSrgbGamma
ColorFiltered(
  colorFilter: ColorFilter.linearToSrgbGamma(),
  child:  Image.asset(
    'assets/images/sabar.webp',
    fit: BoxFit.cover,
    width: 150,
    height: 150,
  ),
)
  
//.matrix
ColorFiltered(
  colorFilter: ColorFilter.matrix(<double>[
    1,0,0,0,110,
    0,1,0,0,110,
    0,0,1,0,110,
    0,0,0,1,0
  ]),
  child:  Image.asset(
    'assets/images/sabar.webp',
    fit: BoxFit.cover,
    width: 150,
    height: 150,
  ),
),
复制代码
```

------

同样 `ColorFiltered` 并不局限于 `Image` 组件。如下将 `FlutterUnit` 整体使用 `ColorFiltered` 进行灰色处理，这样`全应用`都会有着色效果，说明 `ColorFiltered` 可以处理任意组件。这在某些场景下还是非常有用的。

| -                                                            | -                                                            | -                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/52360186bb6a4847a840dbd3768fac99~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fcf0efab6e1848c9aba21cc50700bd33~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbce52f20f14412db4dd53352129449a~tplv-k3u1fbpfcp-watermark.awebp) |

```dart
ColorFiltered(
  colorFilter: ColorFilter.matrix(<double>[
    0.2126, 0.7152, 0.0722, 0, 0,
    0.2126, 0.7152, 0.0722, 0, 0,
    0.2126, 0.7152, 0.0722, 0, 0,
    0,      0,      0,      1, 0,
  ]),
  child: FlutterUnit(),
),
复制代码
```

其实有了 `matrix` 可以操作颜色变换，就会有很多可发挥的空间，它又可以作用于任何组件，做出全局的怀旧色什么的也不是不可以。`matrix` 这东西，有着无限的可能。

------

##### 3.ColorFiltered 组件源码实现

可能会有人担心性能什么的，其实看了这么多的 `SingleChildRenderObjectWidget` ，我们也能知道一些特点。`ColorFiltered` 是通过 `_ColorFilterRenderObject` 实现的。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e133298b8b14f678d26cf66e78f81fb~tplv-k3u1fbpfcp-watermark.awebp)

在绘制时添加了一层 `ColorFilterLayer` ，进行着色处理。这就像是在眼前放一个`红色的膜片`，看到世界上所有的东西都带红色一样，而不是膜片拥有`改变世界的能力`，把世界上所有的东西真正被染成了红色。

`ColorFiltered` 也是一样，它只是一个遮罩层，并非所有的组件都一一被染成红色，不用顾虑太多。`Opacity`、`ClipRect`、`Transform` 这类功能性的组件都是通过添加对应的层实现功能的，所以 `ColorFilterLayer` 也就是一个普通的 `SingleChildRenderObjectWidget` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5ef2db9fd3504e1f8f5753e7977b2f06~tplv-k3u1fbpfcp-watermark.awebp)

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/07306ba94d874719a046d4cb48f92d08~tplv-k3u1fbpfcp-watermark.awebp)


作者：张风捷特烈
链接：https://juejin.cn/post/7001670852437606408
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
