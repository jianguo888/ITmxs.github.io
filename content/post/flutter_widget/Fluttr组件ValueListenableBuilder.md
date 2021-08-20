---
title: "Fluttr组件ValueListenableBuilder"
date: 2021-08-16T08:48:19+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---



该Widget的显示内容 会与ValueListenable保持同步，ValueListenable是dart的接口，该接口用于 保持value变化的监听者。这里涉及到了一套dart本身提供的通知机制，机制结构如下：



![img](https:////upload-images.jianshu.io/upload_images/6567305-bbee3e1ca4c7c6c2.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

ChangeNotifier



使用ValueListenableBuilder时需要提供两个 必填参数 ：ValueListenable参数和ValueWidgetBuilder 参数。ValueListenable参数用于注册值变化回调。ValueWidgetBuilder用于同步value变化的UI。

## 使用参数

### ValueListenable

通过该参数 添加了一个变化的回调，回调是setState()。这样当发送了值变化的通知后，该widget就会setState（），就实现了与value的同步。通常使用的是ValueNotifer，因为ValueNotifer可以快捷的发送通知。

### ValueWidgetBuilder

该参数是方法参数，代码如下：



```csharp
Widget Function(BuildContext context, T value, Widget child)
//context :上下文
//value：变化的值，我们就是根据这个value实现了 数据内容同步，
//child:该参数并不常用。类似动画的使用，可以给某个widget传入一个child，让该widget显示child。
```

## 使用案例

### 兄弟之间通信

比如点击按钮之后，文本发生变化。



```java
import 'package:flutter/material.dart';

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  ValueNotifier<int> valueNotifier;

  @override
  void initState() {
    super.initState();
    //初始化通知器
    valueNotifier = ValueNotifier(0);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('兄弟widget通信'),
      ),
      body: Center(
        child: ValueListenableBuilder(
          ///为什么要用ValueNotifier呢，因为需要快捷的发送通知
          valueListenable: valueNotifier,
          ///动态更新UI
          builder: (context, value, _) {
            return Text(
              '$value',
            );
          },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          ///更新数据，发送通知
          valueNotifier.value = ++valueNotifier.value;
        },
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}
```

上面代码的处理流程：A：ValueListenableBuilder监听了Value的变化，当Value变化内部触发setState
 B：按钮点击valueNotifier发送通知
 效果如下：



![img](https:////upload-images.jianshu.io/upload_images/6567305-76936ebdcbd46555.png?imageMogr2/auto-orient/strip|imageView2/2/w/1080/format/webp)

效果

## 小结

ValueListenableBuilder是简易版的Provider，只监听了一个value的变化。Provider除了监听变化之外，还增加了拦截、多value，子获取祖先value等功能。



参考链接

https://juejin.cn/post/6881846793173205006

https://www.youtube.com/watch?v=s-ZG-jS5QHQ&list=PLjxrf2q8roU23XGwz3Km7sQZFTdB996iG&index=31
