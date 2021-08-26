---
title: "Flutter组件OverFlowBox,SizedOverFlowBox"
date: 2021-08-26T08:42:49+08:00
draft: true
---

#### 1.认识 OverflowBox 组件

我们都知道，一般布局组件，由于父区域的约束，其子节点区域都会被限制在内父区域之内。但有时我们有需求让子组件脱离区域限制，溢出父区域。而这就是 `OverflowBox` 的价值所在。源码中的介绍是: 让组件的约束有别于父节点的约束，可以允许组件溢出父部件。


它继承自 `SingleChildRenderObjectWidget` 。可以设置宽高区域及对其方式。

你如何让子 `SizedBox` 突破限制呢，如下通过 `OverflowBox` 设置一个 `200*50` 的强约束。这样子 `SizedBox` 则会按照 `OverflowBox` 施加的约束进行布局。从效果上来看，子 `SizedBox` 就突破了外层约束的限制。

#### 1.认识 SizedOverflowBox 组件

`SizedOverflowBox` 组件的特点是：

- 1. 它可以指定自身特定的尺寸
- 1. 它会将原始的约束传递给孩子
- 1. 它的孩子可以溢出

他们最大的区别在于： `OverflowBox` 会指定新约束传递给孩子，而 `SizedOverflowBox` 则将原始约束传递给孩子。

#### 2.SizedOverflowBox 组件的使用

如下，在一个灰色盒子左上角，有一个小红圈，其中心与盒子左上角对齐。可以看出在效果上，小红圈 `溢出`了灰色盒子的区域。实现方式是，灰色盒子内部对齐方式 `Alignment.topLeft` ，`SizedOverflowBox` 对齐方式 `Alignment.center` 。

![image-20210826084952749](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210826084952749.png)

```dart
class CustomSizedOverflowBox extends StatelessWidget{
  
  @override
  Widget build(BuildContext context) {
    return Container(
      alignment: Alignment.topLeft,
      color: Colors.grey.withAlpha(88),
      width: 50,
      height: 50,
      child: buildChild(),
    );
  }

  Widget buildChild() {
    return SizedOverflowBox(
      alignment: Alignment.center,
      size: Size.zero,
      child: Container(
        decoration: const BoxDecoration(
          color: Colors.red,
          shape: BoxShape.circle,
        ),
        width: 15,
        height: 15,
      ),
    );
  }
}
```



