---
title: "Flutterweb高级导航"
date: 2021-09-27T16:48:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

在 YouTube 上订阅

# Flutter Web 高级导航

在本教程中，我们将介绍 Web 开发的导航设置

今天我们将介绍 Flutter web 的 URL 导航。如果您的设置正确，大部分导航都是免费的。我们想涵盖以下内容：

1. 使用浏览器中的 url 导航到 Web 应用程序中的页面
2. 导航到 Web 应用程序中的页面并使用浏览器中的参数

我们想要通过导航实现的是确保整个页面不会被换出，而是我们只换出页面的内容而保持工具栏完好无损。为了实现这一点，我们将利用 .builder 属性`MaterialApp`。这为您提供`BuildContext`了 onGenerateRoute 返回的以及子小部件。我们可以使用那个孩子并将其放置在我们定义的内容区域中。





## 浏览器网址导航

为了开始导航，我们将更改路线名称并为所有路线名称添加一个正斜杠。在移动设备上，由于深层链接行为，这不是一个好主意。它实际上首先导航到“/”，然后导航到您提供的名称，然后在返回堆栈上留下两条路线并带有 1 个导航。这不是重点。打开 route_names.dart 文件并在所有名称前添加一个斜杠。

```dart
const String HomeRoute = '/home';
const String AboutRoute = '/about';
const String EpisodesRoute = '/episodes';
const String EpisodeDetailsRoute = '/episode';
```

然后，我们可以`LayoutTemplate`通过删除`Navigator`并将其替换为`Widget`我们通过构造函数传入的子项来更新小部件。

```dart
class LayoutTemplate extends StatelessWidget {
  final Widget child;
  const LayoutTemplate({Key key, this.child}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ResponsiveBuilder(
      builder: (context, sizingInformation) => Scaffold(
        drawer: sizingInformation.deviceScreenType == DeviceScreenType.Mobile
            ? NavigationDrawer()
            : null,
        backgroundColor: Colors.white,
        body: CenteredView(
          child: Column(
            children: <Widget>[
              NavigationBar(),
              Expanded(
                child: child,
              )
            ],
          ),
        ),
      ),
    );
  }
}
```

我们传入的任何小部件都将在`NavigationBar`小部件下全屏显示。这对我们来说意味着我们可以利用 的 builder 属性`MaterialApp`并将子级传递`LayoutTemplate`给路由生成的 。我们还将向 提供与`MaterialApp`对`Navigator`.

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      ...
      builder: (context, child) => LayoutTemplate(
        child: child,
      ),
      navigatorKey: locator<NavigationService>().navigatorKey,
      onGenerateRoute: generateRoute,
      initialRoute: HomeRoute,
    );
  }
}
```

如果现在运行代码，您将看到浏览器中的 url 设置为http://localhost:55865/#/home。完美，易于阅读和分享的东西。如果您在浏览器中将主页更改为剧集，那么您将看到剧集视图。这意味着我们只能使用浏览器 url 在应用程序中进行直接导航。单击关于视图，然后按后退按钮。如您所见，这也非常有效。

## 浏览器网址参数

接下来，我们将查看从输入的字符串中解析 url 参数并将其传递给您想要的视图。对于我们的用例，我们将导航到一个剧集并将其 id 传递给剧集详细信息视图。我们想通过两种方式做到这一点。通过输入 url 和内部导航直接导航到页面。

### 直接导航到带有 URL 参数的页面

运行代码并在“#”符号后键入 /episode?id=1。你会看到什么都没有发生，因为没有为路由器定义这样的路径。我们将使用扩展名`String`来返回`RoutingData`. 让我们从第`RoutingData`一个开始。在数据模型下创建一个名为 routing_data.dart 的新文件

```dart
class RoutingData {
  final String route;
  final Map<String, String> _queryParameters;

  RoutingData({
    this.route,
    Map<String, String> queryParameters,
  }) : _queryParameters = queryParameters;

  operator [](String key) => _queryParameters[key];
}
```

它将存储路线（它将与路线名称匹配），queryParameters 作为 a `Map`，我们将覆盖 [] 运算符以使用查询键索引到该地图。接下来让我们创建扩展方法。在 extensions 文件夹中创建一个名为 string_extensions.dart 的新文件。我们将其命名为 StringExtension 并将其添加到`String`类中。

```dart
import 'package:the_basics/datamodels/routing_data.dart';

extension StringExtension on String {
  RoutingData get getRoutingData {
    var uriData = Uri.parse(this);
    print('queryParameters: ${uriData.queryParameters} path: ${uriData.path}');
    return RoutingData(
      queryParameters: uriData.queryParameters,
      route: uriData.path,
    );
  }
}
```

现在我们可以转到 router.dart 文件并进行修改。我们将从在函数调用开始时获取routingData 开始。然后我们将使用 routingData 上的 route 属性来开启。此函数中的最后一件事是在大小写与`EpisodeDetailsRoute`. 然后我们将使用该 id 并将其传递到 EpisodeDetails 视图中。

```dart
Route<dynamic> generateRoute(RouteSettings settings) {
  var routingData = settings.name.getRoutingData; // Get the routing Data
  switch (routingData.route) { // Switch on the path from the data
    case HomeRoute:
      return _getPageRoute(HomeView(), settings);
    case AboutRoute:
      return _getPageRoute(AboutView(), settings);
    case EpisodesRoute:
      return _getPageRoute(EpisodesView(), settings);
    case EpisodeDetailsRoute:
      var id = int.tryParse(routingData['id']); // Get the id from the data.
      return _getPageRoute(EpisodeDetails(id: id), settings);
    default:
      return _getPageRoute(HomeView(), settings);
  }
}
```

如果您运行代码并再次导航到 /episode?id=1 ，您应该看到一个加载指示器，然后列出了剧集数据。UI 仅显示缩略图和标题。

### 在内部导航到 url

我们要确保在内部导航到 EpisodeDetails 时，url 匹配。因此，我们将更新`NavigationService`以接收 queryParameters。这样我们就可以附加传入的页面路由并将查询参数添加到 uri 中。打开`NavigationService`并更新`navigateTo`功能。

```dart
  Future<dynamic> navigateTo(String routeName,
      {Map<String, String> queryParams}) {
    if (queryParams != null) {
      routeName = Uri(path: routeName, queryParameters: queryParams).toString();
    }
    return navigatorKey.currentState.pushNamed(routeName);
  }
```

当 queryParams 不为空时，我们使用路径构造一个新的 URI 并将其设置为 routeName。然后我们可以继续更新`EpisodeList`以添加`GestureDetector`小部件，以便我们可以处理 onTap 功能。由于我们需要索引导航到`EpisodeDetails`我们必须使用作为地图传入的剧集，因此我们在地图函数中也获得了一个索引项。将 episode_list.dart 文件更新为以下内容。

```dart
class EpisodesList extends StatelessWidget {
  ...
  @override
  Widget build(BuildContext context) {
    return ViewModelProvider<EpisodeListViewModel>.withConsumer(
      viewModel: EpisodeListViewModel(),
      builder: (context, model, child) => Wrap(
        spacing: 30,
        runSpacing: 30,
        children: <Widget>[
          ...episodes
              .asMap()
              .map((index, episode) => MapEntry(
                    index,
                    GestureDetector(
                      child: EpisodeItem(model: episode),
                      onTap: () => model.navigateToEpisode(index),
                    ),
                  ))
              .values
              .toList()
        ],
      ),
    );
  }
}
```

在这里，我们只是将剧集映射到`MapEntry`. 我们提供要作为值显示的索引和小部件。然后我们将映射结果的值显示为孩子。我们要做的最后一件事是更新 episode_list_view_model.dart 文件以添加该`navigateToEpisode`函数。打开文件，添加`NavigationService`前面提到的和 函数。

```dart
class EpisodeListViewModel extends ChangeNotifier {
  final NavigationService _navigationService = locator<NavigationService>();

  void navigateToEpisode(int index) {
    _navigationService
        .navigateTo(EpisodeDetailsRoute, queryParams: {'id': index.toString()});
  }
}
```

如果您现在运行代码，导航到剧集并选择一个剧集，您将看到剧集详细信息和我们之前创建的匹配 url。这就是网络导航。您现在可以使用它在路由器级别提供参数并将其传递给您的视图以在视图模型中使用以检索所需的信息。感谢阅读，如果您对更多网络基础知识有任何建议，请告诉我，我会将其添加到列表中。
