---
title: "Flutter组件dismissible"
date: 2021-08-17T13:06:29+08:00
draft: true
---



今天来看一个和滑动相关的组件：`Dismissible` 。该组件可以通过滑动来使条目移除。先来看一下它最简单的使用。



指定注意的是：`Dismissible` 组件滑动移除只是 UI 的效果，实际的数据并未被移除。为了保证`数据`与 `UI` 的一致性，我们`一般`在移除后，会同时移除对应的数据，并进行重建，

##  提供“滞留”提示

顾名思义，我们的应用允许用户将列表项滑出列表，但是应用可能没有向用户给出视觉提示，告诉他们操作时发生了什么。要给出提示，表明我们正在删除列表项，就需要在他们将列表项滑出屏幕的时候，展示一个“滞留”提示。这个例子中，我们使用了一个红色背景。

出于这个目的，我们为 `Dismissible` 设置了一个 `background` 参数。

```dart
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

// MyApp is a StatefulWidget. This allows updating the state of the
// widget when an item is removed.

// MyApp是一个StatefulWidget。这样，我们就能够在列表项被移除的时候，更新Widget的状态。

class MyApp extends StatefulWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  MyAppState createState() {
    return MyAppState();
  }
}

class MyAppState extends State<MyApp> {
  final items = List<String>.generate(20, (i) => 'Item ${i + 1}');

  @override
  Widget build(BuildContext context) {
    const title = 'Dismissing Items';

    return MaterialApp(
      title: title,
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: Scaffold(
        appBar: AppBar(
          title: const Text(title),
        ),
        body: ListView.builder(
          itemCount: items.length,
          itemBuilder: (context, index) {
            final item = items[index];
            return Dismissible(
              // Each Dismissible must contain a Key. Keys allow Flutter to
              // uniquely identify widgets.
              // 每个Dismissible实例都必须包含一个Key。Key让Flutter能够对Widgets做唯一标识。
              key: Key(item),
              // Provide a function that tells the app
              // what to do after an item has been swiped away.
              // 我们还需要提供一个函数，告诉应用，在项目被移出后，要做什么。
              onDismissed: (direction) {
                // Remove the item from the data source.
                // 从数据源中移除项目
                setState(() {
                  items.removeAt(index);
                });

                // Then show a snackbar.
                ScaffoldMessenger.of(context)
                    .showSnackBar(SnackBar(content: Text('$item dismissed')));
              },
              // Show a red background as the item is swiped away.
              // 列表项被滑出时，显示一个红色背景(Show a red background as the item is swiped away)
              background: Container(color: Colors.red),
              child: ListTile(
                title: Text(item),
              ),
            );
          },
        ),
      ),
    );
  }
}
```

##### 1、 background 和 secondaryBackground

`Dismissible` 组件滑动时，我们可以指定背景组件。如果只设置 `background` ，那么左滑和右滑背景都是一样的，如下左图绿色背景。如果设置 `background` 和 `secondaryBackground` ，则左滑背景为 `background` ，右滑背景为 `secondaryBackground` ，如下右图

代码实现也 很简单，指定 `background` 和 `secondaryBackground` 对于组件即可。如下 `tag1` 和 `tag2` 处理。

```dart
Widget _buildItems(BuildContext context, int index) {
  return Dismissible(
    key: ValueKey(data[index]),
    background: buildBackground(), // tag1
    secondaryBackground: buildSecondaryBackground(), // tag2
    child: ItemBox(
      info: data[index],
    ),
    onDismissed: (direction) =>_onDismissed(direction,index),
  );
}

Widget buildBackground(){
  return Container(
    color: Colors.green,
    alignment: Alignment(-0.9, 0),
    child: Icon(
      Icons.check,
      color: Colors.white,
    ),
  );
}

Widget buildSecondaryBackground(){
  return Container(
    alignment: Alignment(0.9, 0),
    child: Icon(
      Icons.close,
      color: Colors.white,
    ),
    color: Colors.red,
  );
}
复制代码
```

------

##### 2. confirmDismiss 回调

从源码中可以看出 `confirmDismiss` 的类型为 `ConfirmDismissCallback` 。它是一个函数类型，可以回调出 `DismissDirection` 对象，返回 `bool` 值。可以看出这个回调是一个`异步方法`，所以我们可以处理一下异步事件。

```dart
---->[Dismissible#confirmDismiss 声明]----
final ConfirmDismissCallback? confirmDismiss;

typedef ConfirmDismissCallback = Future<bool?> Function(DismissDirection direction);
复制代码
```

如下左图中，滑动结束后，`等待两秒`再执行后续逻辑。效果上来看条目会在两秒后移除。也就说明 `onDismissed` 是在 `confirmDismiss` 异步方法完成后才被调用的。

该回调有一个 `Future<bool?>` 类型的返回值，返回 `false` 则表示不移除条目。如下右图中，绿色背景下`不会`移除条目，红色背景下`会`移除条目。就可以通过该返回值进行控制。

代码实现如下, `tag1` 处设置 `confirmDismiss` 属性。返回值是看 `direction` 是否不是 `startToEnd`，即 `从左向右滑动` 。也就是说， `从左向右滑动` 时，会返回 `false` ，即不消除条目。

```dart
Widget _buildItems(BuildContext context, int index) {
  return Dismissible(
    key: ValueKey(data[index]),
    background: buildBackground(),
    secondaryBackground: buildSecondaryBackground(),
    child: ItemBox(
      info: data[index],
    ),
    onDismissed: (direction) =>_onDismissed(direction,index),
    confirmDismiss: _confirmDismiss, // tag1
  );
}

Future<bool?> _confirmDismiss(DismissDirection direction) async{
  await Future.delayed(Duration(seconds: 2));
  print('_confirmDismiss:$direction');
  return direction!=DismissDirection.startToEnd;
}
复制代码
```

------

##### 3. direction 滑动方向

`direction` 表示滑动的方向，类型是 `DismissDirection` 是枚举，有 `7` 元素。

```dart
enum DismissDirection {
  vertical,
  horizontal,
  endToStart,
  startToEnd,
  up,
  down,
  none
}
复制代码
```

如下左图中，设置 `startToEnd` ,那么从右往左就无法滑动。如下右图中，设置 `vertical` ,那条目就只能在竖直方向响应滑动。不过和列表同向滑动有个问题，条目响应了竖直拖拽手势，那列表的拖拽手势就会`竞技失败`，所以列表是滑不动的。一般来说不会让 `Dismissible` 和列表滑动方向相同，当列表是水平方向滑动， `Dismissible` 可以使用竖直方向滑动。

##### 4. onResize 和 resizeDuration

在竖直列表中，滑动消失时，下方条目会有一个 `上移` 的动画。`resizeDuration` 就代表动画时长，而 `onResize` 会在动画执行的每帧中进行回调。

源码中可以看出 `resizeDuration` 的默认时长为 `300 ms` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ce9d7ded574345aeac6ee9f23bc6aa05~tplv-k3u1fbpfcp-watermark.awebp)

在深入瞄一眼，可以看出会监听动画器执行 `_handleResizeProgressChanged` 。而 `onResize` 就是在此触发的。另外这个动画的曲线是 `_kResizeTimeCurve` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/252a640f3819481288436911f4c44283~tplv-k3u1fbpfcp-watermark.awebp)

```dart
void _handleResizeProgressChanged() {
  if (_resizeController!.isCompleted) {
    widget.onDismissed?.call(_dismissDirection);
  } else {
    widget.onResize?.call();
  }
}

const Curve _kResizeTimeCurve = Interval(0.4, 1.0, curve: Curves.ease);
复制代码
```

------

##### 5.dismissThresholds 和 movementDuration

`dismissThresholds` 表示消失的阈值，类型为`Map<DismissDirection, double>` 映射，也就是说我们可以设置不同滑动方向的容忍度， 默认是 `0.4` 。而 `movementDuration` 代表滑动方向上移动的动画时长。

```dart
const double _kDismissThreshold = 0.4;

final Map<DismissDirection, double> dismissThresholds;
复制代码
```

| 默认效果                                                     | 本案例效果                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f6dcd7ade68d48918c8838b4877a4af0~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/163cdf27233a484ab9ead86266e075ae~tplv-k3u1fbpfcp-watermark.awebp) |

下面代码的效果如上图右侧，当 `startToEnd` 的宇宙设置为 `0.8` , 就会比默认的 `难触发移除事件` 。其中 `movementDuration` 设置为 `3 s` 可以很明显地看出，水平移动的慢速。

```dart
Widget _buildItems(BuildContext context, int index) {
  return Dismissible(
    key: ValueKey(data[index]),
    background: buildBackground(),
    secondaryBackground: buildSecondaryBackground(),
    onResize: _onResize,
    resizeDuration: const Duration(seconds: 2),
    dismissThresholds: {
      DismissDirection.startToEnd: 0.8,
      DismissDirection.endToStart: 0.2,
    },
    movementDuration: const Duration(seconds: 3),
    child: ItemBox(
      info: data[index],
    ),
    direction: DismissDirection.horizontal,
    onDismissed: (direction) => _onDismissed(direction, index),
    confirmDismiss: _confirmDismiss,
  );
}
复制代码
```

------

##### 6. crossAxisEndOffset 交叉轴偏移

如下图，是 `crossAxisEndOffset` 为 `-2` 的效果，在滑动过程中，原条目在交叉轴(此处为纵轴)会发生偏移，偏移量就是 `crossAxisEndOffset * 组件高` 。右图所示，滑动到一般时， `条目 4` 已经上移了一个条目高度。

| 1                                                            | 2                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/64b77e8e0d5a4b1c9616a86cbffc71b5~tplv-k3u1fbpfcp-watermark.awebp) | ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5f145df304804738a35afbac9925bc31~tplv-k3u1fbpfcp-watermark.awebp) |

最后 `dragStartBehavior` 和 `behavior` 就不说了，这种通用的属性大家应该非常清楚。

------

#### 三、从 Dismissible 源码中可以学到什么

`Dismissible` 组件中的 `confirmDismiss` 和 `onDismissed` 两个回调打的一个`组合拳`，还是非常巧妙的，在实际开发中我们也可以通过`异步回调`来处理一些界面效果。我们来看一下源码中的实现： `confirmDismiss` 回调在 `_confirmStartResizeAnimation` 方法中进行调用，

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4467f8e447b14115a2aeb89c15e2123f~tplv-k3u1fbpfcp-watermark.awebp)

在拖拽结束，会先等待 `_confirmStartResizeAnimation` 的执行，且返回 `true` ，才会执行 `_startResizeAnimation` 。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d6a31c8dd519442eb0db237246556ad1~tplv-k3u1fbpfcp-watermark.awebp)

另外一处是在 `_moveController` 动画器执行完毕，如果动画完成，也会执行类似逻辑。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32c0808a27b8462d83f07d134f922122~tplv-k3u1fbpfcp-watermark.awebp)

最后 `onDismissed` 回调会在 `_startResizeAnimation` 中触发。这也就是如何通过一个异步方法，来控制另一个回调的触发。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/16144b20e12147fb8510a1aabdbb11e7~tplv-k3u1fbpfcp-watermark.awebp)

------

`Dismissible 组件`的使用方式到这里就完全介绍完毕，那本文到这里就结束了，谢谢观看，明天见~


作者：张风捷特烈
链接：https://juejin.cn/post/6991639119684173854
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
