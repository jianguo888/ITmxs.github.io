



今天我们来看看 Flutter 中的导航。

但不仅仅是任何无聊的导航。😉

不，女士们，先生们，我们会让这件事变得有趣。

这是一个带有[BottomNavigationBar](https://docs.flutter.io/flutter/material/BottomNavigationBar-class.html)的应用程序：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/multiple-navigators-BottomNavigationBar-overview.png)

我们想要的是每个选项卡都有自己的导航堆栈。这是为了我们在切换标签时不会丢失导航历史记录。例子：



如何构建这个？精简版：

- 创建一个带有`Scaffold`和的应用程序`BottomNavigationBar`。
- 在`Scaffold`正文中，`Stack`为每个选项卡创建一个子项。
- 每个孩子都是一个`Offstage`带有孩子的小部件`Navigator`。
- 不要忘记使用`WillPopScope`.

想要更长更有趣的解释吗？首先，有几个免责声明：

- 本文假设您熟悉 Flutter 中的导航。还有[`Navigator`](https://docs.flutter.io/flutter/widgets/Navigator-class.html)，[`MaterialPageRoute`](https://docs.flutter.io/flutter/material/MaterialPageRoute-class.html)和[`MaterialApp`](https://docs.flutter.io/flutter/material/MaterialApp-class.html)类更多的上下文。
- 使用 可以实现相同的结果[`CupertinoTabScaffold`](https://api.flutter.dev/flutter/cupertino/CupertinoTabScaffold-class.html)，它支持多个导航堆栈。但是`CupertinoTabScaffold`不是很可定制，所以在这里我们将推出一个定制的实现。
- *其中一些代码是实验性的。如果你知道更好的方法，请告诉我。*

好的，让我们开始吧。

## 都是关于导航员的

所有 Flutter 应用程序总是定义一个`MaterialApp`. 通常这位于小部件树的根部：

```
void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.red,
      ),
      home: App(),
    );
  }
}
```

然后，我们可以`App`像这样定义我们的类：

```
// app.dart
class App extends StatefulWidget {
  @override
  State<StatefulWidget> createState() => AppState();
}

class AppState extends State<App> {

  var _currentTab = TabItem.red;

  void _selectTab(TabItem tabItem) {
    setState(() => _currentTab = tabItem);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _buildBody(),
      bottomNavigationBar: BottomNavigation(
        currentTab: _currentTab,
        onSelectTab: _selectTab,
      ),
    );
  }
  
  Widget _buildBody() {
    // TODO: return a widget representing a page
  }
}
```

此类使用`TabItem`定义三个单独选项卡的枚举：

```
// tab_item.dart
import 'package:flutter/material.dart';

enum TabItem { red, green, blue }

const Map<TabItem, String> tabName = {
  TabItem.red: 'red',
  TabItem.green: 'green',
  TabItem.blue: 'blue',
};

const Map<TabItem, MaterialColor> activeTabColor = {
  TabItem.red: Colors.red,
  TabItem.green: Colors.green,
  TabItem.blue: Colors.blue,
};
```

上面的代码还使用两个地图为每个选项卡定义了**标签**和**颜色**。

------

我们再来回顾一下`build()`这个`App`类的方法：

```
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _buildBody(),
      bottomNavigationBar: BottomNavigation(
        currentTab: _currentTab,
        onSelectTab: _selectTab,
      ),
    );
  }
```

`BottomNavigation`是一个自定义小部件，它使用正确的颜色绘制三个选项卡，使用[`BottomNavigationBar`](https://api.flutter.dev/flutter/material/BottomNavigationBar-class.html). 它接受`_currentTab`作为输入并`_selectTab`根据需要调用方法来更新状态。

这是这个类的样子：

```
class BottomNavigation extends StatelessWidget {
  BottomNavigation({@required this.currentTab, @required this.onSelectTab});
  final TabItem currentTab;
  final ValueChanged<TabItem> onSelectTab;

  @override
  Widget build(BuildContext context) {
    return BottomNavigationBar(
      type: BottomNavigationBarType.fixed,
      items: [
        _buildItem(TabItem.red),
        _buildItem(TabItem.green),
        _buildItem(TabItem.blue),
      ],
      onTap: (index) => onSelectTab(
        TabItem.values[index],
      ),
    );
  }

  BottomNavigationBarItem _buildItem(TabItem tabItem) {
    return BottomNavigationBarItem(
      icon: Icon(
        Icons.layers,
        color: _colorTabMatching(tabItem),
      ),
      title: Text(tabName[tabItem]),
    );
  }

  Color _colorTabMatching(TabItem item) {
    return currentTab == item ? activeTabColor[item] : Colors.grey;
  }
}
```

当用户在选项卡之间切换时，这负责绘制`BottomNavigationBar`和调用`onSelectTab`。

------

让我们回到我们的`App`小部件并实现一个`_buildBody()`为`Scaffold`主体返回小部件的方法：

为简单起见，我们可以通过添加`FlatButton`一个回调来推送新页面：

```
Widget _buildBody() {
  return Container(
    color: activeTabColor[TabItem.red],
    alignment: Alignment.center,
    child: FlatButton(
      child: Text(
        'PUSH',
        style: TextStyle(fontSize: 32.0, color: Colors.white),
      ),
      onPressed: _push,
    )
  );
}

void _push() {
  Navigator.of(context).push(MaterialPageRoute(
    // we'll look at ColorDetailPage later
    builder: (context) => ColorDetailPage(
      color: activeTabColor[TabItem.red],
      title: tabName[TabItem.red],
    ),
  ));
}
```

该`_push()`方法是如何工作的？

- `MaterialPageRoute` 负责创建要推送的新路由
- `Navigator.of(context)``Navigator`在小部件树中找到一个上方，并使用它来推送新路线。

你可能想知道，`Navigator`widget 是从哪里来的？

我们还没有自己创建一个，我们`App`类的父类位于`MaterialApp`小部件树的根部。

事实证明，`MaterialApp`在`Navigator`内部创建自己的。

但是，如果我们只是`Navigator.of(context)`用来推送新的路由，就会发生意想不到的事情。

`BottomNavigationBar`随着新页面的出现，整体及其内容滑开。不酷。🤨

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/multiple-navigators-BottomNavigationBar-animation-slide.gif)

其实我们要的是要推的详细信息页面**上**的主页面，但要**保持**在`BottomNavigationBar`底部。

这不起作用，因为`Navigator.of(context)`找到了`BottomNavigatorBar`它自己的祖先。事实上，小部件树看起来像这样：

```
▼ MyApp
 ▼ MaterialApp
  ▼ <some other widgets>
   ▼ Navigator
    ▼ <some other widgets>
     ▼ App
      ▼ Scaffold
       ▼ body: <some other widgets>
       ▼ BottomNavigationBar
```

如果我们打开 Flutter 检查器，它是：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/multiple-navigators-BottomNavigationBar-navigator-widget-tree.png)

如果我们能使用`Navigator`这是**不是**我们的祖先`BottomNavigationBar`，然后按预期的东西会工作。

## Ok Navigator，告诉我你能做什么

解决的办法是包裹`body`我们的`Scaffold`一个新的对象`Navigator`。

但在此之前，让我们介绍三个将用于显示最终 UI 的新类。

第一堂课被称为`TabNavigator`：

```
// 1
class TabNavigatorRoutes {
  static const String root = '/';
  static const String detail = '/detail';
}

// 2
class TabNavigator extends StatelessWidget {
  TabNavigator({this.navigatorKey, this.tabItem});
  final GlobalKey<NavigatorState> navigatorKey;
  final TabItem tabItem;

  // 3
  Map<String, WidgetBuilder> _routeBuilders(BuildContext context,
      {int materialIndex: 500}) {
    return {
      TabNavigatorRoutes.root: (context) => ColorsListPage(
            color: TabHelper.color(tabItem),
            title: TabHelper.description(tabItem),
            onPush: (materialIndex) =>
                _push(context, materialIndex: materialIndex),
          ),
      TabNavigatorRoutes.detail: (context) => ColorDetailPage(
            color: TabHelper.color(tabItem),
            title: TabHelper.description(tabItem),
            materialIndex: materialIndex,
          ),
    };
  }

  // 4
  @override
  Widget build(BuildContext context) {
    final routeBuilders = _routeBuilders(context);
    return Navigator(
      key: navigatorKey,
      initialRoute: TabNavigatorRoutes.root,
      onGenerateRoute: (routeSettings) {
        return MaterialPageRoute(
          builder: (context) => routeBuilders[routeSettings.name](context),
        );
      },
    );
  }

  // 5
  void _push(BuildContext context, {int materialIndex: 500}) {
    var routeBuilders = _routeBuilders(context, materialIndex: materialIndex);

    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => routeBuilders[TabNavigatorRoutes.detail](context),
      ),
    );
  }
}
```

这是如何运作的？

- 在第 1 步中，我们定义了两个路由名称：`/`和`/detail`
- 在第 2 步中，我们为 定义了构造函数`TabNavigator`。这需要 a`navigatorKey`和 a `tabItem`。
- 请注意，`navigatorKey`具有类型`GlobalKey<NavigatorState>`。我们需要它来唯一标识整个应用程序中的导航器（`GlobalKey` [在此处](https://docs.flutter.io/flutter/widgets/GlobalKey-class.html)阅读更多信息）。
- 在第 3 步中，我们定义了一个`_routeBuilders`方法，它将 a`WidgetBuilder`与我们定义的两条路由中的每一条相关联。稍后我们将查看`ColorsListPage`和`ColorDetailPage`。
- 在第 4 步，我们实现了`build()`返回一个新`Navigator`对象的方法。
- 这需要一个`key`和一个`initialRoute`参数。
- 它还有一个`onGenerateRoute`方法，每次需要生成路由时都会调用该方法。这利用了`_routeBuilders()`我们上面定义的方法。
- 在第 5 步，我们定义了一个`_push()`方法，用于推送带有`ColorDetailPage`.

这是`ColorsListPage`课程：

```
class ColorsListPage extends StatelessWidget {
  ColorsListPage({this.color, this.title, this.onPush});
  final MaterialColor color;
  final String title;
  final ValueChanged<int> onPush;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text(
            title,
          ),
          backgroundColor: color,
        ),
        body: Container(
          color: Colors.white,
          child: _buildList(),
        ));
  }

  final List<int> materialIndices = [900, 800, 700, 600, 500, 400, 300, 200, 100, 50];

  Widget _buildList() {
    return ListView.builder(
        itemCount: materialIndices.length,
        itemBuilder: (BuildContext content, int index) {
          int materialIndex = materialIndices[index];
          return Container(
            color: color[materialIndex],
            child: ListTile(
              title: Text('$materialIndex', style: TextStyle(fontSize: 24.0)),
              trailing: Icon(Icons.chevron_right),
              onTap: () => onPush(materialIndex),
            ),
          );
        });
  }
}
```

这个类的目的是显示`ListView`输入的所有颜色阴影`MaterialColor`。`MaterialColor`只不过是`ColorSwatch`有十种不同色调的。

为了完整起见，这里是`ColorDetailPage`：

```
class ColorDetailPage extends StatelessWidget {
  ColorDetailPage({this.color, this.title, this.materialIndex: 500});
  final MaterialColor color;
  final String title;
  final int materialIndex;

  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        backgroundColor: color,
        title: Text(
          '$title[$materialIndex]',
        ),
      ),
      body: Container(
        color: color[materialIndex],
      ),
    );
  }
}
```

这个很简单：它只显示一个页面，其中包含`AppBar`从 input 中选择的颜色阴影`MaterialColor`。它看起来像这样：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/multiple-navigators-BottomNavigationBar-red-detail.png)

## 把东西放在一起

现在我们有了我们的`TabNavigator`，让我们回到我们的`App`并使用它：

```
  final navigatorKey = GlobalKey<NavigatorState>();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: TabNavigator(
        navigatorKey: navigatorKey,
        tabItem: currentTab,
      ),
      bottomNavigationBar: BottomNavigation(
        currentTab: currentTab,
        onSelectTab: _selectTab,
      ),
    );
  }
```

- 首先，我们定义一个`navigatorKey`.
- 然后在我们的`build()`方法中，我们`TabNavigator`用它创建一个并传入`currentTab`.

如果我们现在运行该应用程序，我们可以看到在选择列表项时**push**工作正常，并且`BottomNavigationBar`保持原位。好极了！😀



但是有一个问题。在选项卡之间切换似乎不起作用，因为我们总是在`Scaffold`正文中显示红页。

## 多个导航器

这是因为我们定义了一个新的导航器，但它在所有三个选项卡中**共享**。

**请记住**：我们想要的是每个选项卡的独立导航堆栈！

让我们通过创建**三个**导航器来解决这个问题：

```
class App extends StatefulWidget {
  @override
  State<StatefulWidget> createState() => AppState();
}

class AppState extends State<App> {

  var _currentTab = TabItem.red;
  final _navigatorKeys = {
    TabItem.red: GlobalKey<NavigatorState>(),
    TabItem.green: GlobalKey<NavigatorState>(),
    TabItem.blue: GlobalKey<NavigatorState>(),
  };

  void _selectTab(TabItem tabItem) {
    setState(() => _currentTab = tabItem);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Stack(children: <Widget>[
        _buildOffstageNavigator(TabItem.red),
        _buildOffstageNavigator(TabItem.green),
        _buildOffstageNavigator(TabItem.blue),
      ]),
      bottomNavigationBar: BottomNavigation(
        currentTab: _currentTab,
        onSelectTab: _selectTab,
      ),
    );
  }

  Widget _buildOffstageNavigator(TabItem tabItem) {
    return Offstage(
      offstage: _currentTab != tabItem,
      child: TabNavigator(
        navigatorKey: navigatorKeys[tabItem],
        tabItem: tabItem,
      ),
    );
  }
}
```

一些注意事项：

- 在`AppState`类中，我们定义了全局导航键的映射。我们需要这个来确保我们使用多个导航器。
- 我们的身体`Scaffold`现在是一个`Stack`有三个孩子的人。
- 每个孩子都建立在`_buildOffstageNavigator()`方法中。
- 这使用[`Offstage`](https://docs.flutter.io/flutter/widgets/Offstage-class.html)带有 child的小部件`TabNavigator`。`offstage`如果呈现的选项卡与当前选项卡不匹配，则该属性为 true。
- 我们传递`navigatorKey[tabItem]`给 our`TabNavigator`以确保每个选项卡都有一个单独的导航键。

如果我们编译并运行应用程序，现在一切都按预期工作。我们可以独立地**推送**/**弹出**每个导航器，并且后台导航器保持它们的状态。🚀

## 还有一件事

如果我们在 Android 上运行该应用程序，当我们按下后退按钮时，我们会观察到一个有趣的行为：

![img](https://codewithandrea.com/articles/multiple-navigators-bottom-navigation-bar/images/multiple-navigators-BottomNavigationBar-back-button-not-handled.gif)

该应用程序被关闭，我们回到主屏幕！

这是因为我们还没有指定应该如何处理后退按钮。

让我们解决这个问题：

```
  @override
  Widget build(BuildContext context) {
    return WillPopScope(
      onWillPop: () async =>
          !await navigatorKeys[currentTab].currentState.maybePop(),
      child: Scaffold(
        body: Stack(children: <Widget>[
          _buildOffstageNavigator(TabItem.red),
          _buildOffstageNavigator(TabItem.green),
          _buildOffstageNavigator(TabItem.blue),
        ]),
        bottomNavigationBar: BottomNavigation(
          currentTab: currentTab,
          onSelectTab: _selectTab,
        ),
      ),
    );
  }
```

这是通过[`WillPopScope`](https://docs.flutter.io/flutter/widgets/WillPopScope-class.html)小部件完成的，该小部件控制如何解除路由。从文档`WillPopScope`：

> 注册一个回调以否决用户试图关闭封闭的 [ModalRoute]

请注意如果当前导航器可以弹出，则`onWillPop()`回调如何返回`false`，`true`否则。

如果我们再次运行应用程序，我们可以看到按下后退按钮会关闭所有推送的路由，**只有**再次按下它我们**才会**离开应用程序。



需要注意的一点是，当我们在 Android 上推送新路由时，它会从底部滑入。相反，惯例是在 iOS 上从右侧滑入。



感谢[**Brian Egan**](https://github.com/brianegan)找到了一种让导航器工作的方法。他的想法是使用`Stack`带`Offstage`小部件来保留导航器的状态。

## Wrap up

今天，我们已经了解了扑导航，以及如何结合一个很好的协议`BottomNavigationBar`，`Stack`，`Offstage`和`Navigator`小部件，使多个导航堆栈。

使用`Offstage`小部件可确保我们所有的导航器在保留在小部件树中时保留其状态。这可能会带来一些性能损失，因此如果您选择使用它们，我建议您分析您的应用程序。

注意：`Offstage`我们可以使用 a `CupertinoTabScaffold`，而不是推出我们自己的带有小部件的导航器，它已经内置了对独立导航堆栈的支持。但与 material 不同的是`Scaffold`，`CupertinoTabScaffold`它的 API 有限，您无法使用它来添加浮动操作按钮。





## 迁移指南

迁移前的代码：

*content_copy*

```
BottomNavigationBarItem(
  icon: Icons.add,
  title: Text('add'),
)
```

迁移后的代码：

*content_copy*

```
BottomNavigationBarItem(
  icon: Icons.add,
  label: 'add',
)
```