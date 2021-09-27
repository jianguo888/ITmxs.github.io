---
title: "Flutter架构provider实施指南"
date: 2021-09-27T17:15:34+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

## [语境](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#context)

围绕状态管理的 Flutter 架构一直是一个被抛弃的话题。使用各种技术构建了一些移动应用程序后，我发现无论您选择哪种架构或状态管理解决方案，某些原则都会并且应该存在。在本指南中，我将向您展示代码结构、命名约定、放置文件的位置、如何使用 Provider 包保持组织良好和易于维护。

## [总体概述](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#general-overview)

Provider 基本上是 ScopedModel v2。我已经在 Redux、BLoC 和 ScopedModel 中实现了应用程序，我仍然认为 ScopedModel 是在 Flutter 中构建应用程序的最实用和最直接的方法。即使对于大型或大型应用程序（假设您遵循一些编码指南）。本指南的设置与我的[ScopedModel 指南](https://www.filledstacks.com/post/flutter-architecture-scoped-model-implementation-guide)非常相似。让我们看看我们正在构建的应用程序，以便我们可以了解一些上下文。

![Flutter Provder 应用程序所有屏幕](https://www.filledstacks.com/assets/static/010-all-screens.42db587.33b003e8aedccaf6c90f5b46dc5c0e52.jpg)

该应用程序将具有三个屏幕。登录、主页和发布详细信息。我们将与[JSONPlaceholder API](https://jsonplaceholder.typicode.com/)通信，使用输入的 ID 从登录中获取用户配置文件。获取并显示主页视图上的帖子，并显示帖子详细信息，并附加获取以显示评论。非常基本，但它涵盖了构建向您展示方式的架构所需的一切。

*注意：我不会添加工具栏，所以 iOS 开发者可以在屏幕上从左向右滑动返回。或者，如果您愿意，只需添加一个 AppBar。将其添加到每个 Scaffold 中。*

## [高级架构概述](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#high-level-architecture-overview)

- 每个视图都有自己的模型来扩展 ChangeNotifier。
- 只有当视图的状态发生变化时，才会调用视图的通知侦听器。
- 每个视图只有 2 个状态。空闲和忙碌。视图中包含的任何其他需要逻辑和状态/UI 更新的 UI 将具有与其关联的自己的模型。这样主视图只在主视图状态改变时绘制。
- Providers 不会通过应用程序级别的全局提供程序传递，除非应用程序架构中的多个视图（用户信息）需要它。
- 提供者和服务将使用[get_it](https://pub.dev/packages/get_it)注入。
- 模型只会从服务请求数据并从该数据减少状态。没有其他的。
- 专用服务（只是普通对象，不要混淆初学者）将执行所有实际工作。Api 类将请求和序列化数据。模型只会调用函数来做到这一点。身份验证服务将使用 Api 获取用户详细信息并对其进行跟踪。该模型只是调用该函数并将值传递给它。

这就是高水平。随着我们的进步，请记住这一点。

## [代码设置](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#code-setup)

为了让教程只关注架构，我在 010 下的[教程](https://github.com/FilledStacks/flutter-tutorials)仓库中设置了一个起始项目。如果你想跟我一起学习，请克隆这个仓库。我在这里添加的东西与架构无关。其中一些内容包括：

- 使用 http 客户端从[JSONPlaceholder](https://jsonplaceholder.typicode.com/)获取数据的 Api 服务
- 应用程序中所需的所有数据模型。评论，发布和用户。
- 两种颜色，BackgroundColor 和 CommentColor。
- 两种文字样式，headerStyle、subHeaderStyle
- 提供垂直间距的 Ui Helpers 类
- 带有空构建方法的 LoginView、HomeView 和 PostView。
- 用于呈现单个评论的小部件
- 用于呈现登录标题和文本字段的小部件
- 呈现单个帖子的小部件
- [像这样的](https://www.filledstacks.com/snippet/dependency-injection-in-flutter)定位器文件设置

让我们快速浏览一下结构。lib文件夹分为两个文件夹。核心和用户界面。Core 包含与逻辑关联的所有文件。ui 包含与 ui 关联的所有文件。Core分为三个文件夹。

- 模型：包含所有普通数据模型
- 服务：包含将处理实际业务逻辑的专用文件
- ViewModels：包含每个 Widget 视图的 Provider 模型

UI也分为三个文件夹。

- 共享：包含在多个其他 UI 文件中使用的文件
- 视图：包含应用程序视图的文件
- 小部件：包含太大而无法保存在视图文件中的小部件文件。

## [执行](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#implementation)

我们将逐个查看应用程序并添加我们需要的内容。让我们从登录屏幕开始。转到 core 下的 viewmodels 文件夹并添加一个名为 login_model.dart 的新文件。我们知道我们的视图只有 2 个状态，所以我们将在同一个文件中添加 ViewState 枚举。

```dart
import 'package:flutter/foundation.dart';

/// Represents the state of the view
enum ViewState { Idle, Busy }

class LoginModel extends ChangeNotifier {

}
```

现在把它连接起来。如前所述，我们不会在我们的应用程序开始时在全球范围内提供一堆模型和服务。相反，我们将使用 locator.dart 中的定位器设置注入它。使用定位器将 LoginModel 注册为惰性单例。阅读[本文](https://www.filledstacks.com/snippet/dependency-injection-in-flutter)以快速了解不同类型的注册。

```dart
void setupLocator() {
  locator.registerLazySingleton(() => LoginModel());
}
```

转到 LoginView 并将此模型绑定到提供者/消费者。我们将脚手架包装在一个 Provider 中，我们将使 Provider 的孩子成为消费者，该消费者将 Scaffold 作为它的 builder 孩子返回。

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:provider_architecutre/core/viewmodels/login_model.dart';
import 'package:provider_architecutre/locator.dart';

class LoginView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider<LoginModel>(
      builder: (context) => locator<LoginModel>(),
      child: Consumer<LoginModel>(
        builder: (context, model, child) => Scaffold(),
      ),
    );
  }
}
```

这里我们将 LoginModel 与 LoginView 相关联。让 UI 看起来像下面这样

![Flutter Provider App 登录视图](https://www.filledstacks.com/assets/static/010-login.df42093.b7943b278ad30d1e2648be0828920f4e.jpg)

我们将 Scaffold 的背景颜色设置为 backgroundColor。脚手架的主体将是一个完全拉伸的 Colum，并将其子项对齐到中心。第一个子项是 LoginHeader，第二个子项是登录按钮。使您的构建方法如下所示并导入您需要的所有内容。

```dart
Widget build(BuildContext context) {
    return ChangeNotifierProvider<LoginModel>(
      builder: (context) => locator<LoginModel>(),
      child: Consumer<LoginModel>(
        builder: (context, model, child) => Scaffold(
              backgroundColor: backgroundColor,
              body: Column(
                mainAxisSize: MainAxisSize.max,
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  LoginHeader(),
                  FlatButton(
                      color: Colors.white,
                      child: Text(
                        'Login',
                        style: TextStyle(color: Colors.black),
                      ),
                      onPressed: () async {})
                ],
              ),
            ),
      ),
    );
  }
```

我们需要 LoginView 来接收文本，为此我们需要一个 TextEdittingController，所以它需要是一个有状态的小部件。**将视图转换为有状态小部件**，创建控制器变量并将其传递到 LoginHeader。

```dart
  final TextEditingController controller = TextEditingController();

  Widget build(BuildContext context) {
    ...
    LoginHeader(controller: controller),
    ...
  }
```

接下来，我们将把控制器中的值传递给名为 login 的模型上的函数。创建一个 Future，它在 LoginModel 上返回一个布尔值并传入一个字符串 userIdText。此函数将文本解析为 int 并将其传递给将发出 API 请求的身份验证服务。我们还想指出此时视图的状态。为此，我们将介绍 viewState 属性。每当我们更改此值时，我们都希望通知侦听器，以便重建视图。我们将创建一个 setState 函数来为我们处理这个问题。在通话开始时，我们希望将状态设置为忙碌，当我们完成后回到空闲状态。

```dart
class LoginModel extends ChangeNotifier {
  final AuthenticationService _authenticationService = locator<AuthenticationService>();
  ViewState _state = ViewState.Idle;

  ViewState get state => _state;

  void setState(ViewState viewState) {
    _state = viewState;
    notifyListeners();
  }

   Future<bool> login(String userIdText) async {
    setState(ViewState.Busy);

    var userId = int.tryParse(userIdText);
    var success =  await _authenticationService.login(userId);

    setState(ViewState.Idle);
    return success;
  }
}
```

AuthenticationService 将使用 Api 来获取用户配置文件。如果用户配置文件不为空，我们将返回 true 以表示成功。在 services 下创建 authentication_service.dart 文件并添加`Future<bool>`登录功能。此函数将使用 API 并请求用户配置文件。Api（以及所有其他服务和模型）将使用定位器注入。

```dart
import 'dart:async';

import 'package:provider_architecutre/core/models/user.dart';
import 'package:provider_architecutre/core/services/api.dart';
import 'package:provider_architecutre/locator.dart';

class AuthenticationService {
  // Inject our Api
  Api _api = locator<Api>();

  Future<bool> login(int userId) async {
    // Get the user profile for id
    var fetcheduser = await _api.getUserProfile(userId);

    // Check if success
    var hasUser = fetcheduser != null;

    return hasUser;
  }
}
```

使用定位器注册这两个服务。

```dart
void setupLocator() {
  locator.registerLazySingleton(() => AuthenticationService());
  locator.registerLazySingleton(() => Api());

  locator.registerLazySingleton(() => LoginModel());
}
```

现在，在按钮上 onPressed 的 LoginView 中，我们可以使用控制器中的文本调用登录函数。我们将等待 Future，如果成功，我们将导航到根视图。我们还想在视图繁忙时换出按钮。当视图繁忙时，我们将显示一个进度指示器。

```dart
...

model.state == ViewState.Busy
  ? CircularProgressIndicator()
  : FlatButton(
      color: Colors.white,
      child: Text(
        'Login',
        style: TextStyle(color: Colors.black),
      ),
      onPressed: () async {
        var loginSuccess = await model.login(controller.text);
        if (loginSuccess) {
          // Navigate to the home view
        }
      })
...
```

登录功能和业务逻辑完成。进入下一部分。

### [导航](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#navigation)

登录后，我们必须导航到主页视图。我最喜欢的新导航方式是使用`onGenerateRoute`MaterialApp 提供的功能。您必须为其提供一个函数，该函数将 RouteSettings 作为参数并返回一个`Route<dynamic>`. 为了保持整洁，我们将所有路由放在一个名为 ...... router.dart 的单独文件中，位于 ui 文件夹下。创建路由器文件并放置一个带有我之前描述的签名的静态方法。此函数接收 RouteSettings，其中包含所请求的路线的名称。我们将打开该名称并为适当的视图返回 MaterialPageRoute。我们还将返回任何未定义路由的错误视图。

```dart
class Router {
  static Route<dynamic> generateRoute(RouteSettings settings) {
    switch (settings.name) {
      case '/':
        return MaterialPageRoute(builder: (_) => HomeView());
      case 'login':
        return MaterialPageRoute(builder: (_) => LoginView());
      case 'post':
        return MaterialPageRoute(builder: (_) => PostView());
      default:
        return MaterialPageRoute(builder: (_) {
          return Scaffold(
            body: Center(
              child: Text('No route defined for ${settings.name}'),
            ),
          );
        });
    }
  }
}
```

现在，在您的主文件中，您可以为该`onGenerateRoute`属性提供来自路由器的静态 generateRoute 函数。您可以删除 home 属性并将 initialRoute 设置为“登录”。

```dart
MaterialApp(
    title: 'Flutter Demo',
    theme: ThemeData(),
    initialRoute: 'login',
    onGenerateRoute: Router.generateRoute,
  );
```

在登录按钮 onPressed 的 LoginView 中，我们现在可以通过推送命名路由导航到主视图。

```dart
...

onPressed: () async {
    var loginSuccess = await model.login(controller.text);
    if (loginSuccess) {
      Navigator.pushNamed(context, '/');
    }
  }

...
```

导航完成。视图模型绑定完成。依赖注入连接，代码结构布局。那么现在怎么办？好吧，我们必须重构代码以方便我们添加更多具有相同功能的视图，所以让我们这样做。

### [setState 的共享 ViewModel 功能](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#shared-viewmodel-functionality-for-setstate)

我们所有的模型都将工作相同。我们有一个 state 属性，它告诉我们要在视图中显示什么 UI 布局，以及当它更新时，我们要调用 notifyListeners 以便将其移动到 BaseModel 中。在视图模型下创建一个名为 base_model.dart 的文件。将所有与状态相关的代码从登录模型移到那里。将 enum 定义移动到 viewstate.dart 下 core 中名为 enums 的文件中。

```dart
// base_model.dart
import 'package:flutter/material.dart';

class BaseModel extends ChangeNotifier {
  ViewState _state = ViewState.Idle;

  ViewState get state => _state;

  void setState(ViewState viewState) {
    _state = viewState;
    notifyListeners();
  }
}
// viewstate.dart

/// Represents the state of the view
enum ViewState { Idle, Busy }
```

将 LoginModel 更新为从 BaseModel 而不是 ChangeNotifier 扩展。

```dart
class LoginModel extends BaseModel {
   ...
}
```

现在 LoginModel 更简洁一些，我们可以添加一些额外的功能。我们将处理一种错误情况。如果用户输入的值不是数字，我们将显示错误消息。我们将添加一个新的 String errorMessage 属性来存储我们的消息。

在 tryParse 代码下的登录函数中，我们将检查 userId 是否为空（不是数字）。如果是，我们会将消息和状态设置回空闲并返回 false。

```dart
String errorMessage;

...
var userId = int.tryParse(userIdText);

if(userId == null) {
  errorMessage = 'Value entered is not a number';
  setState(ViewState.Idle);
  return false;
}

...
```

在 Login 视图中，我们现在可以将 errorMessage 传递给 loginHeader 上的验证消息属性。

```dart
...
LoginHeader(
    validationMessage: model.errorMessage,
    controller: _controller),
...
```

如果您现在输入一些文本并尝试登录，您将看到验证消息出现。

### [所有视图的共享设置](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#shared-setup-for-all-views)

大多数视图都需要自己的模型，它们需要有一个根小部件 Provider 和一个采用构建方法的子 Consumer。我们将创建一个通用的 BaseView，它将为我们完成所有这些工作。在 ui 中的 views 文件夹下创建一个新的 base_view.dart 文件。将登录视图中的所有代码移到 Scaffold 之外，并用 T 替换所有 LoginModel 类型。

```dart
class BaseView<T extends BaseModel> extends StatelessWidget {
  final Widget Function(BuildContext context, T value, Widget child) builder;

  BaseView({this.builder});

  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider<T>(
      builder: (context) => locator<T>(),
      child: Consumer<T>(builder: builder),
    );
  }
}
```

在 LoginView 中，用 BaseView 替换所有与 Provider 相关的代码，并将 LoginModel 作为类型传递。

```dart
  @override
  Widget build(BuildContext context) {
    return BaseView<LoginModel>(
        builder: (context, model, child) => Scaffold(
          ...
        ),
    );
  }
```

现在，任何需要模型的小部件都可以使用 BaseView 及其模型类型，并且它会在调用 setState 时对状态更新作出反应 🎉。这基本上是缺少一件的主要架构。我们希望架构设置能够在显示新视图时轻松支持调用函数，而无需将所有内容转换为有状态小部件。为了实现这一点，我们将使用 onInit 将我们的 BaseView 转换为有状态的小部件，将我们的模型传回我们可以执行的回调函数中使用。将 BaseView 转换为有状态小部件并将其传递`Function(T)`给我们，从而将模型返回给我们。我们将模型本地存储在 state 中，并在 initState 调用中检查是否有回调。如果我们这样做，我们将调用它并将我们的模型传递给它。

```dart
class BaseView<T extends ChangeNotifier> extends StatefulWidget {
  final Widget Function(BuildContext context, T value, Widget child) builder;
  final Function(T) onModelReady;

  BaseView({@required this.builder, this.onModelReady});

  @override
  _BaseViewState<T> createState() => _BaseViewState<T>();
}

class _BaseViewState<T extends ChangeNotifier> extends State<BaseView<T>> {
  T model = locator<T>();

  @override
  void initState() {
    if (widget.onModelReady != null) {
      widget.onModelReady(model);
    }
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider<T>(
      builder: (context) => model,
      child: Consumer<T>(builder: widget.builder),
    );
  }
}
```

## [🎉 架构和代码设置完成 🎉](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#-architecture-and-code-setup-complete-)

在这一点上，所有涉及架构的设置都已完成。本教程的其余部分将向您展示如何使用此架构添加更多视图并扩展它。让我们构建 HomeView。

![Flutter Provider App 主页视图](https://www.filledstacks.com/assets/static/010-home.df42093.a684b3265175c998769e310496fcd544.jpg)

让我们从*在 viewmodels 文件夹中*创建 home *model.dart 文件*开始*。****请记住****：_模型所做的只是使用服务来请求数据，然后使用该数据减少状态。*该模型会将 Api 注入其中并公开一个 List. 它还将有一个函数，用于获取帖子并将其设置为等于 posts 属性。

```dart
class HomeModel extends BaseModel {
  Api _api = locator<Api>();

  List<Post> posts;

  Future getPosts(int userId) async {
    setState(ViewState.Busy);
    posts = await _api.getPostsForUser(userId);
    setState(ViewState.Idle);
  }
}
```

然后使用定位器注册您的模型。我们将注册为工厂。这样做的作用是，每次您请求这种类型时，它都会创建一个新的请求。

```dart
...
locator.registerFactory(() => HomeModel());
```

当主视图初始化时，我们想调用 getPosts 函数来为我们的用户获取帖子。为此，我们需要 userId，但目前没有。我们知道此视图和帖子详细信息视图中将需要用户信息以显示作者姓名。我们不想将 AuthenticationService 注入 home 模型，因为这在代码中没有意义。相反，我们将公开一个 User 类型的流控制器，我们将使用 StreamProvier 提供它。更新 AuthenticationService 并添加一个 StreamController 并且当用户不为空时将 fetchedUser 放到该控制器上。

```dart
StreamController<User> userController = StreamController<User>();

Future<bool> login(int userId) async {
    // Not real login, we'll just request the user profile
    var fetcheduser = await _api.getUserProfile(userId);
    var hasUser = fetcheduser != null;
    if (hasUser) {
      userController.add(fetcheduser);
    }

    return hasUser;
  }
```

现在在主文件中，我们将使用 StreamProvider 包装 MaterialApp，并从 AuthenticationService 传入我们的 streamController。我们将提供一个空用户作为初始数据。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return StreamProvider<User>(
        initialData: User.initial(),
        builder: (context) => locator<AuthenticationService>().userController,
        child: MaterialApp(
          title: 'Flutter Demo',
          theme: ThemeData(),
          initialRoute: 'login',
          onGenerateRoute: Router.generateRoute,
        ));
  }
}
```

用户现在可以在应用程序的任何地方使用`Provider.of<User>(context)`. 在 Home 视图中，我们现在可以将根设置为 HomeModel 类型的 BaseView，并且 onModelReady 调用我们的 getPosts 函数。

```dart
class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return BaseView<HomeModel>(
        onModelReady: (model) {
          model.getPosts(Provider.of<User>(context).id);
        },
        builder: (context, model, child) => Scaffold(
          backgroundColor: backgroundColor,
          body: Column()));
  }
}
```

对于主体，我们将根据状态值显示不同的 UI。忙碌时，我们将在屏幕中央显示一个 ProgressIndicator。当空闲时，我们将显示实际的 UI。它只是一个列，第一个子元素是 spaceContainer，第二个是标题，第三个也是标题，另一个是空格，然后是一个带有帖子列表的扩展小部件。

更新您的构建方法并添加如下所示的 listView 函数。

```dart
 @override
  Widget build(BuildContext context) {
    return BaseView<HomeModel>(
        onModelReady: (model) {
          model.getPosts(Provider.of<User>(context).id);
        },
        builder: (context, model, child) => Scaffold(
          backgroundColor: backgroundColor,
            body: model.state == ViewState.Idle
                ? Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    UIHelper.verticalSpaceLarge(),
                    Padding(
                      padding: const EdgeInsets.only(left: 20.0),
                      child: Text('Welcome ${Provider.of<User>(context).name}', style: headerStyle,),
                    ),
                    Padding(
                      padding: const EdgeInsets.only(left: 20.0),
                      child: Text('Here are all your posts', style: subHeaderStyle),
                    ),
                    UIHelper.verticalSpaceSmall(),
                    Expanded(child: getPostsUi(model.posts)),
                  ])
                : Center(child: CircularProgressIndicator())));
  }

  Widget getPostsUi(List<Post> posts) => ListView.builder(
      itemCount: posts.length,
      itemBuilder: (context, index) => PostListItem(
            post: posts[index],
            onTap: () {
              Navigator.pushNamed(context, 'post', arguments: posts[index]);
            },
          ));
```

正如您在 getPostUi 函数中看到的那样。当 PostListItem 被点击时，我们导航到“post”，但我们向它传递了一些参数。参数是 post 模型。我们必须更新我们的路由器以查看参数并将其传递给 Post 视图。打开路由器并更新“post”案例以获取参数并转换为 Post。然后将其传递给 PostView。

```dart
case 'post':
  var post = settings.arguments as Post;
  return MaterialPageRoute(builder: (_) => PostView(post: post));
```

如果您现在运行代码，您将看到登录视图。输入 3 并点击登录。您应该看到忙碌指示器，然后导航到主页视图，在那里它也会显示忙碌，然后在获取帖子时显示 UI。

最后，我们要显示 Post 详细信息并获取一些其他评论。

### [使用具有自己模型的小部件](https://www.filledstacks.com/post/flutter-architecture-my-provider-implementation-guide/#using-a-widget-with-its-own-model)

评论将是一个独立的小部件，具有自己的模型和更新周期。这样，如果评论是实时的，使用类似 firebase 的东西，只有评论小部件会重建和重新绘制而不是整个屏幕。这实际上意味着 PostView 不需要模型，它没有逻辑。所以让我们创建评论模型，然后构建评论小部件，这样我们就可以完成帖子 ui。

在视图模型下创建一个名为 comments_model 的新文件。它与 HomeModel 几乎完全相同。使用 postId 获取评论，设置它，然后调用 setState。

```dart
class CommentsModel extends BaseModel {
  Api _api = locator<Api>();

  List<Comment> comments;

  Future fetchComments(int postId) async {
    setState(ViewState.Busy);
    comments = await _api.getCommentsForPost(postId);
    setState(ViewState.Idle);
  }
}
```

我们已经有了一个 Comments 文件，所以我们只需填写与我们设置的架构相关的部分。我们将使用类型为 CommentsModel 的 BaseView。onModelReady 我们将请求使用传入的 postId 获取评论。 UI 将与 HomeView 相同。忙碌时，循环指示器 else 展开的列表视图。

```dart
class Comments extends StatelessWidget {
  final int postId;
  Comments(this.postId);

  @override
  Widget build(BuildContext context) {
    return BaseView<CommentsModel>(
        onModelReady: (model) => model.fetchComments(postId),
        builder: (context, model, child) => model.state == ViewState.Busy
            ? Center(child: CircularProgressIndicator())
            : Expanded(child: ListView(
                children: model.comments
                    .map((comment) => CommentItem(comment))
                    .toList(),
              )));
  }
}
```

在定位器中注册它。

```dart
locator.registerFactory(() => CommentsModel());
```

现在让我们构建 PostView ui。

![Flutter 提供程序应用发布视图](https://www.filledstacks.com/assets/static/010-post.df42093.6a3125dd7be5051f7ce361d63893316e.jpg)

非常简单的用户界面。顶部有一些间距的标题，作者姓名，帖子正文，然后是我们的评论小部件。这里没什么好看的。

```dart
class PostView extends StatelessWidget {
  final Post post;
  PostView({this.post});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: backgroundColor,
      body: Padding(
        padding: const EdgeInsets.symmetric(horizontal: 20.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: <Widget>[
            UIHelper.verticalSpaceLarge(),
            Text(post.title, style: headerStyle),
            Text(
              'by ${Provider.of<User>(context).name}',
              style: TextStyle(fontSize: 9.0),
            ),
            UIHelper.verticalSpaceMedium(),
            Text(post.body),
            Comments(post.id)
          ],
        ),
      ),
    );
  }
}
```

就是这样。我们有一个完整的架构，绝对可以承受“大应用”的评论。我重新编写了我在 Xamarin 中编写的一个非常大的应用程序，这就是我正在使用的架构。它整洁且易于遵循。大多数模型的代码少于 20 行。服务也很少，因此很容易扩展。

我希望这有帮助。还有很多其他的东西要添加，但这是您想要开始的地方。更进一步，您可以决定要创建的新文件夹，在 viewname 文件夹下将小部件分组以将其保持在一起，将服务拆分为实用程序和服务等。一切由您决定，至少您现在有一个更好的起点。
