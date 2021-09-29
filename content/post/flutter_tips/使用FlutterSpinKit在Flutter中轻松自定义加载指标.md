---
title: "使用FlutterSpinKit在Flutter中轻松自定义加载指标"
date: 2021-09-29T09:19:21+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍使用 FlutterSpinKit 在 Flutter 中轻松自定义加载指标

在我的大部分教程中，当我想显示任何类型的忙碌状态时，我都会使用循环进度指示器。一点也不差，但您可能想要更吸引人或与您的设计相匹配的东西。像下面这样的东西。

![Android SpinKit 示例](https://luckly007.oss-cn-beijing.aliyuncs.com/img/loaders.82a2fbd.60f11dae027eea709f3bfe67e715dc64.gif)

好吧，你很幸运。Flutter SpinKit 就是这样做的。

## 安装

开始将包添加到您的 pubspec

```yaml
flutter_spinkit: ^3.1.0
```

## 用法

有很多指标要使用，所以我不会一一介绍。这篇文章应该作为您可以使用的所有小部件的备忘单。像下面一样设置您的 UI 并进行测试。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        backgroundColor: Colors.grey[800],
          body: Center(
        child: SpinKitWave(color: Colors.white, type: SpinKitWaveType.start),
      )),
    );
  }
}
```

这是供您使用的所有小部件的列表。要进行实验，您可以使用以下任何一种替换 Center 的子项并刷新您的 UI 以查看它是否正常工作。

```dart
SpinKitRotatingCircle(color: Colors.white)
SpinKitRotatingPlain(color: Colors.white)
SpinKitChasingDots(color: Colors.white)
SpinKitPumpingHeart(color: Colors.white)
SpinKitPulse(color: Colors.white)
SpinKitDoubleBounce(color: Colors.white)
SpinKitWave(color: Colors.white, type: SpinKitWaveType.start)
SpinKitWave(color: Colors.white, type: SpinKitWaveType.center)
SpinKitWave(color: Colors.white, type: SpinKitWaveType.end)
SpinKitThreeBounce(color: Colors.white)
SpinKitWanderingCubes(color: Colors.white)
SpinKitWanderingCubes(color: Colors.white, shape: BoxShape.circle)
SpinKitCircle(color: Colors.white)
SpinKitFadingFour(color: Colors.white)
SpinKitFadingFour(color: Colors.white, shape: BoxShape.rectangle)
SpinKitFadingCube(color: Colors.white)
SpinKitCubeGrid(size: 51.0, color: Colors.white)
SpinKitFoldingCube(color: Colors.white)
SpinKitRing(color: Colors.white)
SpinKitDualRing(color: Colors.white)
SpinKitRipple(color: Colors.white)
SpinKitFadingGrid(color: Colors.white)
SpinKitFadingGrid(color: Colors.white, shape: BoxShape.rectangle)
SpinKitHourGlass(color: Colors.white)
SpinKitSpinningCircle(color: Colors.white)
SpinKitSpinningCircle(color: Colors.white, shape: BoxShape.rectangle)
SpinKitFadingCircle(color: Colors.white)
SpinKitPouringHourglass(color: Colors.white)
```