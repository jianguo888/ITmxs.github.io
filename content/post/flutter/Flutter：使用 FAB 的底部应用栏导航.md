今天我们将看到如何在 Flutter 中添加一个`FloatingActionButton`带有选项的(FAB) `BottomAppBar`。我们将构建这个 UI：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/BottomAppBar-Navigation-FAB-animation.gif)

我们在这里的具体目标是拥有一个[`BottomAppBar`](https://docs.flutter.io/flutter/material/BottomAppBar-class.html)行为与[`BottomNavigationBar`](https://docs.flutter.io/flutter/material/BottomNavigationBar-class.html). 也就是说，在任何给定时间都有多个选项卡，其中恰好有一个选定的选项卡。

你可能想知道，为什么不`BottomNavigationBar`直接使用呢？

虽然在技术上可以将 a`FloatingActionButton`与 a “对接” `BottomNavigationBar`，但这在实践中效果不佳。更多关于这个下面。

现在，让我们看看如何使用`BottomAppBar`.

[![Flutter 应用内购买和订阅变得简单](https://codewithandrea.com/img/sponsor-revenue-cat.png)所有人都可以免费使用 Andrea 编写代码。通过查看这个赞助商来帮助我保持这种状态：**Flutter 应用内购买和订阅变得简单。**RevenueCat 提供可靠的应用内购买基础设施，无需管理服务器。支持 iOS、Android 和 Stripe。免费试用。](https://github.com/RevenueCat/purchases-flutter)

## [添加居中的 FAB](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/#adding-a-centered-fab)

创建一个新的 Flutter 项目后，我们得到了默认的示例应用程序。这已经包括一个`FloatingActionButton`用于递增计数器。

我们可以添加`BottomAppBar`到我们`Scaffold.bottomNavigationBar`，像这样：

```
return Scaffold(
  appBar: AppBar(
    title: Text(widget.title),
  ),
  floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
  floatingActionButton: FloatingActionButton(
    onPressed: () { },
    tooltip: 'Increment',
    child: Icon(Icons.add),
    elevation: 2.0,
  ),
  bottomNavigationBar: BottomAppBar(
    child: Row(
      mainAxisSize: MainAxisSize.max,
      mainAxisAlignment: MainAxisAlignment.spaceBetween,
      children: <Widget>[],
    ),
    notchedShape: CircularNotchedRectangle(),
    color: Colors.blueGrey,
  ),
);
```

请注意我们如何设置`Scaffold.floatingActionButtonLocation`将`FloatingActionButtonLocation.centerDocked`FAB“停靠”到`BottomAppBar`.

我们还设置在 FAB 下方`notchedShape: CircularNotchedRectangle()`添加一个漂亮的缺口形状`BottomAppBar`。

如果我们在这个阶段运行应用程序，我们会得到：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/BottomAppBar-Navigation-FAB.png)

## [添加带有导航的选项卡](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/#adding-tabs-with-navigation)

除了我们的 FAB，我们还想添加选项卡，以便我们可以在我们的应用程序中查看不同的页面。

为此，我们可以创建一个自定义`FABBottomAppBar`来处理以下内容：

- 显示 2 个或 4 个选项卡（我们的设计是对称的）。
- 突出显示并跟踪当前选定的选项卡。
- 每次选择选项卡时都会触发回调，以便父级可以更新当前页面。

那么我们的最小 API 会是什么样子`FABBottomAppBar`呢？让我们来看看：

```
class FABBottomAppBarItem {
  FABBottomAppBarItem({this.iconData, this.text});
  IconData iconData;
  String text;
}

class FABBottomAppBar extends StatefulWidget {
  final List<FABBottomAppBarItem> items;
  final ValueChanged<int> onTabSelected;
  
  @override
  State<StatefulWidget> createState() => FABBottomAppBarState();
}

class FABBottomAppBarState extends State<FABBottomAppBar> {
  int _selectedIndex = 0;

  _updateIndex(int index) {
    widget.onTabSelected(index);
    setState(() {
      _selectedIndex = index;
    });
  }
  
  // TODO: build method here
}
```

首先，我们定义一个`FABBottomAppBarItem`包含 an`IconData`和 a 的类`String`。这表示单个选项卡项。

然后，我们创建一个`FABBottomAppBar`小部件。这需要指定项目列表和回调 ( `onTabSelected`)。

`FABBottomAppBar`本身是一个`StatefulWidget`，因为它需要跟踪所选选项卡并相应地更新其外观。

在`FABBottomAppBarState`类中，我们定义了一个`_selectedIndex`，它在我们调用时更新`_updateIndex`。

接下来，我们需要定义`build`方法，在其中添加所有项目：

```
@override
Widget build(BuildContext context) {
  List<Widget> items = List.generate(widget.items.length, (int index) {
    return _buildTabItem(
      item: widget.items[index],
      index: index,
      onPressed: _updateIndex,
    );
  });

  return BottomAppBar(
    child: Row(
      mainAxisSize: MainAxisSize.max,
      mainAxisAlignment: MainAxisAlignment.spaceAround,
      children: items,
    ),
  );
}
```

一些注意事项：

- **第 3 到 9 行**：这里我们使用列表生成器来创建选项卡项，传入正确的项、索引和宽度。
- **第 11 到 17 行**：我们创建一个`BottomAppBar`包含`Row`我们定义的项目的a 。我们使用`MainAxisSize.max`和`MainAxisAlignment.spaceAround`确保使用全宽并且项目等距。

然后，我们实现该`_buildTabItem`方法。这里是：

```
Widget _buildTabItem({
  FABBottomAppBarItem item,
  int index,
  ValueChanged<int> onPressed,
}) {
  Color color = _selectedIndex == index ? widget.selectedColor : widget.color;
  return Expanded(
    child: SizedBox(
      height: widget.height,
      child: Material(
        type: MaterialType.transparency,
        child: InkWell(
          onTap: () => onPressed(index),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Icon(item.iconData, color: color, size: widget.iconSize),
              Text(
                item.text,
                style: TextStyle(color: color),
              )
            ],
          ),
        ),
      ),
    ),
  );
}
```

在第 6 行，我们检查所选索引是否与当前选项卡索引匹配，并相应地选择活动/非活动颜色。

在第 13 到 26 行，我们定义了一个`InkWell`包裹在`Material`小部件内的。这为我们`onTap`提供了一个手势识别器，并在点击小部件时提供了材质飞溅效果。

我们的孩子`InkWell`是 a `Column`，其中包含一个`Icon`and `Text`，两者都配置了来自 input 的数据`FABBottomAppBarItem`。

整个结果被包裹在一个`Expanded`小部件中。这保证了每个项目在 parent 内具有相同的宽度`Row`。

我们可以测试`FABBottomAppBar`使用它作为我们创造`bottomNavigationBar`我们的`Scaffold`，并提供了四个项目：

```
bottomNavigationBar: FABBottomAppBar(
  onTabSelected: _selectedTab,
  items: [
    FABBottomAppBarItem(iconData: Icons.menu, text: 'This'),
    FABBottomAppBarItem(iconData: Icons.layers, text: 'Is'),
    FABBottomAppBarItem(iconData: Icons.dashboard, text: 'Bottom'),
    FABBottomAppBarItem(iconData: Icons.info, text: 'Bar'),
  ],
),
```

结果如下：

![img](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/images/BottomAppBar-Navigation-FAB-4-items.png)

我们现在可以切换选项卡，并在每次切换时获得回调。

## [细节出汗](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/#sweating-the-details)

迄今为止取得了令人鼓舞的进展。但是有一些事情我们可以改进，以使我们的产品`FABBottomAppBar`更加“生产就绪”。

在上面的示例代码中，我们对以下内容进行了硬编码：

- `BottomAppBar`：`height`，`backgroundColor`和`notchedShape`
- 图标大小
- 活动/非活动选项卡颜色

此外，中间的标签图标感觉有点太接近 FAB 本身。在中间添加一些间距会很好。

更好的是，为什么不在 FAB 下方添加一个小的可选文本标题，与选项卡的文本基线对齐？

让我们一口气解决所有这些问题。这是我们的最终代码`FABBottomAppBar`：

```
import 'package:flutter/material.dart';

class FABBottomAppBarItem {
  FABBottomAppBarItem({this.iconData, this.text});
  IconData iconData;
  String text;
}

class FABBottomAppBar extends StatefulWidget {
  FABBottomAppBar({
    this.items,
    this.centerItemText,
    this.height: 60.0,
    this.iconSize: 24.0,
    this.backgroundColor,
    this.color,
    this.selectedColor,
    this.notchedShape,
    this.onTabSelected,
  }) {
    assert(this.items.length == 2 || this.items.length == 4);
  }
  final List<FABBottomAppBarItem> items;
  final String centerItemText;
  final double height;
  final double iconSize;
  final Color backgroundColor;
  final Color color;
  final Color selectedColor;
  final NotchedShape notchedShape;
  final ValueChanged<int> onTabSelected;

  @override
  State<StatefulWidget> createState() => FABBottomAppBarState();
}

class FABBottomAppBarState extends State<FABBottomAppBar> {
  int _selectedIndex = 0;

  _updateIndex(int index) {
    widget.onTabSelected(index);
    setState(() {
      _selectedIndex = index;
    });
  }

  @override
  Widget build(BuildContext context) {
    List<Widget> items = List.generate(widget.items.length, (int index) {
      return _buildTabItem(
        item: widget.items[index],
        index: index,
        onPressed: _updateIndex,
      );
    });
    items.insert(items.length >> 1, _buildMiddleTabItem());

    return BottomAppBar(
      shape: widget.notchedShape,
      child: Row(
        mainAxisSize: MainAxisSize.max,
        mainAxisAlignment: MainAxisAlignment.spaceAround,
        children: items,
      ),
      color: widget.backgroundColor,
    );
  }

  Widget _buildMiddleTabItem() {
    return Expanded(
      child: SizedBox(
        height: widget.height,
        child: Column(
          mainAxisSize: MainAxisSize.min,
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            SizedBox(height: widget.iconSize),
            Text(
              widget.centerItemText ?? '',
              style: TextStyle(color: widget.color),
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildTabItem({
    FABBottomAppBarItem item,
    int index,
    ValueChanged<int> onPressed,
  }) {
    Color color = _selectedIndex == index ? widget.selectedColor : widget.color;
    return Expanded(
      child: SizedBox(
        height: widget.height,
        child: Material(
          type: MaterialType.transparency,
          child: InkWell(
            onTap: () => onPressed(index),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                Icon(item.iconData, color: color, size: widget.iconSize),
                Text(
                  item.text,
                  style: TextStyle(color: color),
                )
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

请注意现在我们如何指定 a `centerItemText`，它将位于 FAB 正下方。如果这是空的 or `null`，我们渲染一个空的`Text`。

这是最终结果：

![img](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/images/BottomAppBar-Navigation-FAB-5-items.png)

## [底部导航栏呢？](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/#what-about-bottomnavigationbar?)

我曾尝试将 FAB 停靠在 a 中`BottomNavigationBar`，但我发现了一些限制：

- 除了创建一个`BottomNavigationBarItem`. 这是不可取的，因为`BottomNavigationBarItem`它本身就是一个选项卡，可以点击。
- `BottomNavigationBar`不支持`notchedShape`。

使用`BottomAppBar`需要更多的代码，但更灵活，因为它`Row`在引擎盖下使用，并且可以根据需要进行配置。

*顺便说一句，如果您想了解更多有关`BottomNavigationBar`以及如何使用它来启用多个独立导航器的信息，请务必阅读我之前的文章：*

- [Flutter 案例研究：具有 BottomNavigationBar 的多个导航器](https://codewithandrea.com/articles/multiple-navigators-bottom-navigation-bar/)

## [完整的源代码](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/#full-source-code)

我[在 GitHub 上](https://github.com/bizz84/bottom_bar_fab_flutter)开源了我的完整示例。🙏

随意在您的项目中重用它。😎

## [轻按 FAB 时显示选项](https://codewithandrea.com/articles/bottom-bar-navigation-with-fab/#revealing-options-when-the-fab-is-tapped)

这将在下一篇文章中介绍：