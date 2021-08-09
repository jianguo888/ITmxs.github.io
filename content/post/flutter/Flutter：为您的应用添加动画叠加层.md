这是我关于`BottomAppBar`FAB 的教程的第二部分（第一部分[在这里](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/)）。今天我们将看到如何在按下 FAB 时显示选项。

我们将看到如何在 Flutter 中添加叠加层，以及为什么这是一种可以添加到工具箱中的有价值的技术。



## 按下 FAB 时显示按钮

在本教程中，我们将添加代码以启用以下动画：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/BottomAppBar-Navigation-FAB-animation.gif)

动画本身最初取自StackOverflow 上的[这个示例](https://stackoverflow.com/questions/46480221/flutter-floating-action-button-with-speed-dail)。

我已将示例代码捆绑到`FabWithIcons`一个新的自定义小部件中，我们可以将其直接分配给我们的`Scaffold.floatingActionButton`：

```
import 'package:flutter/material.dart';

// https://stackoverflow.com/questions/46480221/flutter-floating-action-button-with-speed-dail
class FabWithIcons extends StatefulWidget {
  FabWithIcons({this.icons, this.onIconTapped});
  final List<IconData> icons;
  ValueChanged<int> onIconTapped;
  @override
  State createState() => FabWithIconsState();
}

class FabWithIconsState extends State<FabWithIcons> with TickerProviderStateMixin {
  AnimationController _controller;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      vsync: this,
      duration: const Duration(milliseconds: 250),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      mainAxisSize: MainAxisSize.min,
      children: List.generate(widget.icons.length, (int index) {
        return _buildChild(index);
      }).toList()..add(
        _buildFab(),
      ),
    );
  }

  Widget _buildChild(int index) {
    Color backgroundColor = Theme.of(context).cardColor;
    Color foregroundColor = Theme.of(context).accentColor;
    return Container(
      height: 70.0,
      width: 56.0,
      alignment: FractionalOffset.topCenter,
      child: ScaleTransition(
        scale: CurvedAnimation(
          parent: _controller,
          curve: Interval(
              0.0,
              1.0 - index / widget.icons.length / 2.0,
              curve: Curves.easeOut
          ),
        ),
        child: FloatingActionButton(
          backgroundColor: backgroundColor,
          mini: true,
          child: Icon(widget.icons[index], color: foregroundColor),
          onPressed: () => _onTapped(index),
        ),
      ),
    );
  }

  Widget _buildFab() {
    return FloatingActionButton(
      onPressed: () {
        if (_controller.isDismissed) {
          _controller.forward();
        } else {
          _controller.reverse();
        }
      },
      tooltip: 'Increment',
      child: Icon(Icons.add),
      elevation: 2.0,
    );
  }

  void _onTapped(int index) {
    _controller.reverse();
    widget.onIconTapped(index);
  }
}
```

这是如何运作的？

- **第 4 行：**我们定义`FabWithIcons`为 a，`StatefulWidget`因为它需要 an`AnimationController`来管理动画状态。
- **第 5 到 7 行**：我们传递图标列表和点击图标时的回调。这是一种类型，`ValueChanged<int>`以便我们可以指定被点击的图标的索引。
- **第 12 到 22 行**：我们将 a 添加[`TickerProviderStateMixin`](https://docs.flutter.io/flutter/widgets/TickerProviderStateMixin-class.html)到我们的状态类中，并初始化 an [`AnimationController`](https://docs.flutter.io/flutter/animation/AnimationController-class.html)，并将其传递`this`给`vsync`参数。*有关动画 API 的完整概述，请参阅[Sergi & Replace](https://twitter.com/SergiAndReplace)[撰写的](https://sergiandreplace.com/flutter-animations-using-animationcontroller-and-introducing-tweens/)有关 Flutter 动画的[文章](https://sergiandreplace.com/flutter-animations-using-animationcontroller-and-introducing-tweens/)。*
- **第 25 到 35 行**：在我们的`build`方法中，我们生成一个图标列表，并附加 a`FloatingActionButton`作为最后一个元素。
- **第 37 到 61 行**：我们为给定索引处的项目定义一个小 ( `mini: true`) `FloatingActionButton`，并将一个[`ScaleTransition`](https://docs.flutter.io/flutter/widgets/ScaleTransition-class.html)小部件链接到我们的动画控制器。这就是使 FAB 进出动画的原因。
- **第 63 到 76 行**：我们创建主 FAB，并`onPressed`根据需要使用处理程序更新我们的动画控制器。
- **第78行至81**：选择选项时，我们隐藏所有图标（通过`_controller.reverse()`），并通知父项已被选中。

让我们尝试使用它。

在我们的主页中，我们添加这个方法来创建一个带有三个图标的 FAB：

```
Widget _buildFab(BuildContext context) {
  final icons = [ Icons.sms, Icons.mail, Icons.phone ];
  return FabWithIcons(
    icons: icons,
    onIconTapped: (index) {},
  );
}
```

然后，我们可以像这样在我们的内部调用它`Scaffold`：

```
floatingActionButton: _buildFab(context)
```

如果我们运行该应用程序，我们会发现这看起来不对。但是，如果我们注释掉`floatingActionButtonLocation`在我们这行`Scaffold`的FAB移动到默认位置，并利用我们不再干涉`BottomAppBar`。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/BottomAppBar-Navigation-FAB-docked-right-group.png)

这里发生了什么？

嗯，我们`FABWithIcons`的比普通的 FAB 高，因为它有额外的图标。试图将其作为停靠项放在 a`BottomAppBar`中效果不佳。我怀疑 Flutter SDK 试图通过将 FAB 一直推到底部来补偿额外的高度，但这会导致视觉伪影。

该怎么办？

## 输入叠加

如果我们可以在我们的 中使用标准 FAB 作为单个小部件`BottomAppBar`，但是当我们需要显示额外的图标时切换到一个完全独立的层，该怎么办？

Flutter 提供了一个名为的小部件`Overlay`，非常适合这种情况。从`Overlay`该类的文档中：

> 可以独立管理的条目堆栈。Overlays 通过将它们插入到叠加层的 Stack 中，让独立的子小部件将视觉元素“浮动”在其他小部件的顶部。叠加层让这些小部件中的每一个都可以使用`OverlayEntry`对象管理它们对叠加层的参与。

这听起来是我们所需要的。但是我们如何使用它呢？

SDK 文档显示我们需要创建一个`OverlayEntry`对象，以便我们可以保存自定义`FABWithIcons`. 我们可以通过定义这个方法来做到这一点：

```
void addToOverlay(OverlayEntry entry) async {
  Overlay.of(context).insert(entry);
}
```

然而，这是一些**命令式**代码，而我们的应用程序 UI 是**声明式**定义的。我们如何调和两者？

事实证明，让叠加层工作需要相当多的工作，而正确地实现它们并不简单。

幸运的是，[Matt Carroll](https://github.com/matthew-carroll)已经在他[关于 Feature Discovery 的 Fluttery 视频中](https://www.youtube.com/watch?v=Xm0ELlBtNWM)探讨了这个问题，并创建了一些有用的布局助手小部件。

出于本教程的目的，我们将借用并使用他的一些代码。这是整个 Overlay 布局代码：

```
// code from: https://github.com/matthew-carroll/flutter_ui_challenge_feature_discovery
// TODO: Use https://github.com/matthew-carroll/fluttery/blob/master/lib/src/layout_overlays.dart
import 'package:flutter/material.dart';

class AnchoredOverlay extends StatelessWidget {
  final bool showOverlay;
  final Widget Function(BuildContext, Offset anchor) overlayBuilder;
  final Widget child;

  AnchoredOverlay({
    this.showOverlay,
    this.overlayBuilder,
    this.child,
  });

  @override
  Widget build(BuildContext context) {
    return new Container(
      child: new LayoutBuilder(builder: (BuildContext context, BoxConstraints constraints) {
        return new OverlayBuilder(
          showOverlay: showOverlay,
          overlayBuilder: (BuildContext overlayContext) {
            RenderBox box = context.findRenderObject() as RenderBox;
            final center = box.size.center(box.localToGlobal(const Offset(0.0, 0.0)));
            return overlayBuilder(overlayContext, center);
          },
          child: child,
        );
      }),
    );
  }
}

class OverlayBuilder extends StatefulWidget {
  final bool showOverlay;
  final Function(BuildContext) overlayBuilder;
  final Widget child;

  OverlayBuilder({
    this.showOverlay = false,
    this.overlayBuilder,
    this.child,
  });

  @override
  _OverlayBuilderState createState() => new _OverlayBuilderState();
}

class _OverlayBuilderState extends State<OverlayBuilder> {
  OverlayEntry overlayEntry;

  @override
  void initState() {
    super.initState();

    if (widget.showOverlay) {
      WidgetsBinding.instance.addPostFrameCallback((_) => showOverlay());
    }
  }

  @override
  void didUpdateWidget(OverlayBuilder oldWidget) {
    super.didUpdateWidget(oldWidget);
    WidgetsBinding.instance.addPostFrameCallback((_) => syncWidgetAndOverlay());
  }

  @override
  void reassemble() {
    super.reassemble();
    WidgetsBinding.instance.addPostFrameCallback((_) => syncWidgetAndOverlay());
  }

  @override
  void dispose() {
    if (isShowingOverlay()) {
      hideOverlay();
    }

    super.dispose();
  }

  bool isShowingOverlay() => overlayEntry != null;

  void showOverlay() {
    overlayEntry = new OverlayEntry(
      builder: widget.overlayBuilder,
    );
    addToOverlay(overlayEntry);
  }

  void addToOverlay(OverlayEntry entry) async {
    print('addToOverlay');
    Overlay.of(context).insert(entry);
  }

  void hideOverlay() {
    print('hideOverlay');
    overlayEntry.remove();
    overlayEntry = null;
  }

  void syncWidgetAndOverlay() {
    if (isShowingOverlay() && !widget.showOverlay) {
      hideOverlay();
    } else if (!isShowingOverlay() && widget.showOverlay) {
      showOverlay();
    }
  }

  @override
  Widget build(BuildContext context) {
    return widget.child;
  }
}

class CenterAbout extends StatelessWidget {
  final Offset position;
  final Widget child;

  CenterAbout({
    this.position,
    this.child,
  });

  @override
  Widget build(BuildContext context) {
    return new Positioned(
      top: position.dy,
      left: position.dx,
      child: new FractionalTranslation(
        translation: const Offset(-0.5, -0.5),
        child: child,
      ),
    );
  }
}
```

Matt[在他的视频中](https://www.youtube.com/watch?v=Xm0ELlBtNWM)详细解释了这段代码。

在这里，我只是展示了如何将它用于我们的目的。

底线：我们可以更新我们的`_buildFab`方法如下：

```
Widget _buildFab(BuildContext context) {
  final icons = [ Icons.sms, Icons.mail, Icons.phone ];
  return AnchoredOverlay(
    showOverlay: true,
    overlayBuilder: (context, offset) {
      return CenterAbout(
        position: Offset(offset.dx, offset.dy - icons.length * 35.0),
        child: FabWithIcons(
          icons: icons,
          onIconTapped: _selectedFab,
        ),
      );
    },
    child: FloatingActionButton(
      onPressed: () { },
      tooltip: 'Increment',
      child: Icon(Icons.add),
      elevation: 2.0,
    ),
  );
}
```

此代码用于`AnchoredOverlay`为我们的`FabWithIcons`小部件构建单独的叠加层。`CenterAbout`用于相对于 FAB 的位置定位我们的叠加层。为了`FabWithIcons`正确定位我们，我们用图标的数量乘以每个图标的大小（35 pt）来抵消它。

**注意**：在第 4 行，我们通过`showOverlay: true`. 这是为了使叠加层中的 FAB 始终可见并堆叠在`BottomAppBar`. 这是可以的，因为`FABWithIcons`当图标隐藏时，原始 FAB 和里面的那个看起来是一样的。

如果您用于`AnchoredOverlay`构建自己的 UI，您可以为`showOverlay`参数保存一个状态变量，并根据需要切换它。

## 而已

我们已经看到了如何使用动画作为叠加显示我们的自定义 UI，以便我们可以`BottomAppBar`毫无问题地将我们的自定义 FAB 停靠在 a 中。

使用提供的布局助手，向您的应用程序添加覆盖变得非常容易：

- 将所有叠加 UI 代码移动到自定义小部件类中。
- 将所需的小部件包裹在`AnchoredOverlay`.
- 用于`CenterAbout`相对于原始小部件定位您的叠加层。

我[在 GitHub 上](https://github.com/bizz84/bottom_bar_fab_flutter)开源了我的完整示例。🙏