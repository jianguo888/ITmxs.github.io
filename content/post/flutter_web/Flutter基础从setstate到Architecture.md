---
title: "Flutter基础从setstate到Architecture"
date: 2021-09-27T17:18:30+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

掘金

在本教程中，我将介绍如何在 Flutter 中处理常见的异步情况，而不是将架构抛在问题上。



您将学习如何将事情做的更简单，以及如何将其发展成可维护和复杂的东西……随着时间的推移……**如果**它需要发展。

我看到了一些关于 Flutter 必须提供的深度的问题，因为大多数教程都专注于构建 UI 和使事情看起来很漂亮。这绝对是这种情况，我也是这样做的。这很有趣，而且来自原生开发，使用 Flutter 之类的东西非常令人兴奋。但是我开始有点担心首先教给开发人员的东西，尤其是初学者。

我看到使用 BloC 或 Redux 的人对 StackOverflow 提出问题，他们甚至不知道流的基础知识，或者他们必须订阅，或者为什么使用 setState 或继承小部件的概念。你会看到他们构建了一个 viewModel，在那里调用 setState，然后在一个动作中分派 setState，因为他们的 UI 没有更新。这有点令人担忧。

我是编写良好的应用程序的爱好者，并试图为手头的问题编写最好的代码。我不会到处乱扔架构。有时它是不需要的，

## 在我们的应用程序中获取状态

我们首先将 Home 小部件从无状态更改为有状态小部件。然后我们将添加我们将在 initState 覆盖中调用的 Future。Future 将等待 1 秒然后返回数据。

```dart
// Return a list of data after 1 second to emulate network request
Future<List<String>> _getListData() async {
  await Future.delayed(Duration(seconds: 1));
  return List<String>.generate(10, (index) => '$index title');
}
```

那么最基本的处理方法是什么？我们将在本地存储状态中的值列表，并在获得新值时调用 setState。

```dart
List<String> _pageData = List<String>();

@override
void initState() {
  _getListData().then((data) =>
      setState(() {
        _pageData = data;
      }));
  super.initState();
}
```

让我们添加一些 UI 来显示列表，以便我们可以看到数据。将您的构建功能更改为此。

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
    body: ListView.builder(
      itemCount: _pageData.length,
      itemBuilder: (buildContext, index) => Container(
        margin: EdgeInsets.all(5.0),
        height: 50.0,
        color: Colors.grey[700],
        child: Text(_pageData[index]),
      ),
    ),
  );
}
```

此时，您将在屏幕上显示结果列表。我将添加一些样式以使事情更容易查看然后我们可以继续。将 ListItemUi 从构建器方法中移到它自己的函数中。将脚手架背景颜色设置为灰色 [900] 并为您的 _getListItemUi 使用以下代码。

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
    backgroundColor: Colors.grey[900],
    body: ListView.builder(
      itemCount: _pageData.length,
      itemBuilder: (buildContext, index) =>_getListItemUi(index)
  ));
}

Widget _getListItemUi(int index) {
  return  Container(
        margin: EdgeInsets.all(5.0),
        height: 50.0,
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(5.0),
          color: Colors.grey[600]
        ),
        child: Center(
          child: Text(_pageData[index], style: TextStyle(color: Colors.white,),
      ),
        ),
    );
}
```

现在回到实际教程。我们将在设置简单的同时处理状态反馈。那么我们有哪些州呢？

**Busy**：当我们忙于某事时，我们必须告诉用户。在获取数据时，我们将显示一个进度指示器。

**DataRetrieved**（已处理）：数据到达时向用户展示。

**ErrorOccurred**：出现问题时显示消息

**NoData**：向用户表明请求成功但他们还没有数据可显示。

让我们先解决忙。我们将时间延迟增加到 2 秒，以便我们可以看到正在运行的 UI。我们可以使用检查数据是否为空的私有属性来确定状态。基于此值返回加载指示器或列表视图。我们必须在开始时确保数据为空，因此删除初始化代码并将其保留为空。

```dart
List<String> _pageData;

 bool get _fetchingData => _pageData == null;

...

@override
Widget build(BuildContext context) {
  return Scaffold(
    backgroundColor: Colors.grey[900],
    body: _fetchingData
    ? Center(child: CircularProgressIndicator())
    : ListView.builder(
      itemCount: _pageData.length,
      itemBuilder: (buildContext, index) =>_getListItemUi(index)
  ));
}
```

在重新启动应用程序时进行这些更改后，您应该会看到忙碌指示 2 秒钟，然后结果会弹出。很好。进入下一个状态。让我们做错误状态。我们在这种方法上受到限制，但我们会使其发挥作用。当出现错误时，我们将填充一个包含错误消息的项目。我们将在 .then 调用后捕获错误。我们还必须更新未来以返回错误。我们会先这样做。

```dart
Future<List<String>> _getListData({bool hasError = false}) async {
  await Future.delayed(Duration(seconds: 2));

  if(hasError) {
    return Future.error('An error occurred while fetching the data. Please try again later.');
  }

  return List<String>.generate(10, (index) => '$index title');
}
```

然后我们可以在 initFunction 调用中传入 true 并捕获错误。

```dart
@override
void initState() {
  _getListData(hasError: true).then((data) =>
      setState(() {
        _pageData = data;
      }))
      .catchError((error) =>
      setState((){
        _pageData = [error];
      }));
  super.initState();
}
```

惊人的。当您现在重新启动应用程序时，您应该会看到一点点加载，然后弹出错误。最后一个状态是没有返回任何信息。我们将再次更新 Future 以接收另一个额外的布尔值，它将为我们返回一个空列表。

```dart
Future<List<String>> _getListData({
  bool hasError = false,
  bool hasData = true}) async {
  await Future.delayed(Duration(seconds: 2));

  if(hasError) {
    return Future.error('An error occurred while fetching the data. Please try again later.');
  }

  if(!hasData) {
    return List<String>();
  }

  return List<String>.generate(10, (index) => '$index title');
}
```

如果返回的数据中没有项目，我们将添加一个带有消息的项目。在我们取回数据的地方添加这个逻辑。

```dart
_getListData(hasError: true).then((data) =>
    setState(() {
      if(data.length == 0) {
        data.add('No data found for your account. Add something and check back.');
      }
      _pageData = data;
    }))
    .catchError((error) =>
    setState((){
      _pageData = [error];
    }));
```

看那个。仅使用 setState 并获得异步行为的防弹实现。这么简单的事情不需要包或架构。这就是它存在的原因。那么这种方法的局限性是什么？

**我们必须始终调用 setState 以确保状态更新**。这不是响应式实现，意思是，我们必须编写代码来更新 setState 中的 UI。这意味着更多的代码、更多的事情要跟踪和扩展或添加新的逻辑部分将需要更多的 setStates。

Flutter 为我们提供了一种很好的方式来处理所有这些，而无需使用有状态的小部件和设置状态。上面的所有代码都可以用一个小部件代替。

## 未来建设者

FutureBuilder 小部件是一个接受 Future 的小部件，并允许您根据该 Future 的状态或信息返回 UI。您可以为它提供一个构建器，您将在其中接收快照（有关 Future 状态的信息），并根据该快照返回适当的 UI。

我们首先将 Home 小部件改回无状态小部件并删除 initState 调用。我们还将把所有的 UI 逻辑移到未来的构建器方法中。

将代码从正文移动到名为 _getDataUi() 的函数中，这样我们就不会丢失它。将小部件改回无状态小部件，并从构建方法返回脚手架主体中的 FutureBuilder。将视图更新为如下所示。

```dart
class Home extends StatelessWidget {
  ...
  ...

 @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[900],
      body: FutureBuilder()
    );
  }

 Widget _getDataUi() {
  return _fetchingData
      ? Center(child: CircularProgressIndicator())
      : ListView.builder(
          itemCount: _pageData.length,
          itemBuilder: (buildContext, index) => _getListItemUi(index));
  }
  ...
}
```

让我们将未来传递给 FutureBuilder 并为其提供一个空的构建器。像这样。

```dart
 @override
Widget build(BuildContext context) {
  return Scaffold(backgroundColor: Colors.grey[900], body: FutureBuilder(
    future: _getListData(),
    builder: (buildContext, snapshot) {

    },
  ));
}
```

现在我们开始营业了！我们可以返回我们想要的 UI，并在未来的构建器中确定本地状态。我们将从 2 个基本状态开始，busy 和 dataFetched。从 _getDataUI 函数中剪切代码并从构建器函数中返回它。

当没有数据时返回忙碌状态，当有数据时返回 ListView 构建器。使用 snapshot.data 而不是 _pageData 变量，因此您还必须将其传递给 _getListItemUi 函数。

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(backgroundColor: Colors.grey[900], body: FutureBuilder(
    future: _getListData(),
    builder: (buildContext, snapshot) {
      if(!snapshot.hasData) {
        return Center(child: CircularProgressIndicator());
      }

       var listItems = snapshot.data;
            return ListView.builder(
                itemCount: listItems.length,
                itemBuilder: (buildContext, index) => _getListItemUi(index, listItems));
    },
  ));
}

// Add listItems as a parameter and pass it in.
Widget _getListItemUi(int index, List<String> listItems) {
  return Container(
    margin: EdgeInsets.all(5.0),
    height: 50.0,
    decoration: BoxDecoration(
        borderRadius: BorderRadius.circular(5.0), color: Colors.grey[600]),
    child: Center(
      child: Text(
        listItems[index],
        style: TextStyle(
          color: Colors.white,
        ),
      ),
    ),
  );
}
```

现在我们可以处理错误和空数据。在顶部添加错误处理并在返回列表视图之前添加空检查。这两个将具有相同的样式，因此创建一个名为 _getInformationMessage() 的方法。这个函数接收一些文本并显示出来。

```dart
Widget _getInformationMessage(String message) {
  return Center(
      child: Text(
    message,
    textAlign: TextAlign.center,
    style: TextStyle(fontWeight: FontWeight.w900, color: Colors.grey[500]),
  ));
}
```

然后将 FutureBuilder 更改为如下所示。

```dart
FutureBuilder(
  future: _getListData(),
  builder: (buildContext, snapshot) {

    // error ui
    if(snapshot.hasError) {
      return _getInformationMessage(snapshot.error);
    }

    // Busy fetchind data
    if (!snapshot.hasData) {
      return Center(child: CircularProgressIndicator());
    }

    var listItems = snapshot.data;

    // When empty data is returned
    if(listItems.length == 0) {
      return _getInformationMessage('No data found for your account. Add something and check back.');
    }

    // Build list if we have data
    return ListView.builder(
        itemCount: listItems.length,
        itemBuilder: (buildContext, index) => _getListItemUi(index, listItems));
  }
));
```

有了它，您应该在不使用 setState 的情况下拥有所有相同的功能。很酷吧。您可以通过将不同的值传递给 _getListData 函数来测试状态。传入 hasError: true 并重新启动应用程序，您将看到错误消息，与 hasData: false 相同。以这种方式使用它仍然有一些限制。

您无法重新运行未来的构建器并再次遍历所有状态。让我告诉你我的意思。比如说，我们得到了数据，但意识到它很旧，所以我们想要更新它。很常见的场景。我们将向脚手架添加一个 FloatingActionButton，并在点击时关闭 Future 以显示限制。

在脚手架中的 backgroundColor 上方添加一个 floatingActionButton。

```dart
...
floatingActionButton: FloatingActionButton(onPressed: () {
    // We want to refresh, but this actually does nothing. Which is the limitation
    _getListData();
  }),
backgroundColor:
...
```

那是一个毫无意义的按钮，但这只是为了证明一点。如果我们想重新运行未来以进行刷新，那么我们将无法做到。不是这种方法。为了得到这样的东西，我们需要 UI 根据我们传递给它的状态值一致地响应状态变化。为此，我们可以使用 Stream 和 StreamBuilder。

## 流生成器

此小部件允许您根据使用流发送给它的值返回 UI。这将使我们受益的方式是，我们可以随时发送状态值，并且小部件将显示我们为该状态定义的 UI。这意味着我们可以将它设置回忙碌状态，获取新数据，然后告诉流我们已经完成并显示更新的数据。

首先，我们将使用枚举来表示状态。在 home 文件的顶部，在类之外创建一个，称为 HomeViewState。忽略 Error 因为流控制器允许您向流添加错误。

```dart
enum HomeViewState { Busy, DataRetrieved, NoData }
```

向 Stream 添加值的方法是使用 StreamController。创建一个流控制器，将 FutureBuilder 更改为 StreamBuilder，并将 future 参数更改为流。

```dart
// Top of the file
import 'dart:async';

....
// Above build method
final StreamController<HomeViewState> stateController = StreamController<HomeViewState>();

// Change the FutureBuilder to StreamBuilder
 body: StreamBuilder(
    stream: stateController.stream,
 ...
```

如您所见，构建器仍然可以与相同的 UI 一起使用。唯一的区别是，我们将查看枚举值，而不是使用数据来做出决定。保持错误处理不变，只更新其余部分。

```dart
 builder: (buildContext, snapshot) {

  if(snapshot.hasError) {
    return _getInformationMessage(snapshot.error);
  }

  // Use busy indicator if there's no state yet, and when it's busy
  if (!snapshot.hasData || snapshot.data ==HomeViewState.Busy) {
    return Center(child: CircularProgressIndicator());
  }

  // use explicit state instead of checking the lenght
  if(snapshot.data ==HomeViewState.NoData) {
    return _getInformationMessage('No data found for your account. Add something and check back.');
  }

  var listItems = snapshot.data;
  return ListView.builder(
      itemCount: listItems.length,
      itemBuilder: (buildContext, index) => _getListItemUi(index, listItems));
}
```

现在还剩下一些东西。

1. 我们必须从不同的地方获取 ListView 构建器的 listItems，它不再在快照中。
2. 我们必须发出流值
3. 当我们登陆页面时，我们必须运行未来。为此，我们需要回到有状态的小部件并使用 init 函数。

如果您在 VS Code 中安装了 Flutter + Dart 扩展，那么它应该很快。在 StatelessWidget 类类型上按 Ctrl + Shift + R 并选择 convert to stateless 。Mac 上的 Option + Shift + R。现在您有了一个有状态的 Home 小部件，我们可以继续。

我们将首先将项目列表添加回类并在 init 函数中调用 _getListData。我们还将通过来自 _getListDataFuture 的流发出正确的值。

```dart
final StreamController<HomeViewState> stateController = StreamController<HomeViewState>();
// Add our listItems at the top
List<String> listItems;

// Call our _getListData in the initState function
@override
void initState() {
  _getListData();
  super.initState();
}

// Update our future
Future _getListData({bool hasError = false, bool hasData = true}) async {
  stateController.add(HomeViewState.Busy);
  await Future.delayed(Duration(seconds: 2));

  if (hasError) {
    return stateController.addError(
        'An error occurred while fetching the data. Please try again later.');
  }

  if (!hasData) {
    return stateController.add(HomeViewState.NoData);
  }

  listItems = List<String>.generate(10, (index) => '$index title');
  stateController.add(HomeViewState.DataRetrieved);
}
```

请记住从 StreamBuilder 的构建器函数中删除本地 listItems 值。

我们现在要关注的部分是调用 Future 时会发生什么。一开始我们广播busy状态。如果有错误，我们将它添加到流中，如果我们没有数据，我们广播它，最后我们告诉流数据已被处理。此时，即使我们的业务逻辑代码和 UI 还在同一个文件中，也已经解耦了。

如果你现在运行代码，一切都应该是一样的，只是增加了一个。如果您点击 FloatingActionButton 视图将刷新 :) 。现在一切都已解耦并且在一个流中我们可以创建一个“架构”，我的意思是将您的文件拆分。

## 从逻辑上拆分文件

此时 UI 的代码与业务逻辑完全分离。对于那些考虑架构和代码维护的人来说，下一个合乎逻辑的步骤是拆分我们的文件。

我们将我们的文件拆分为一个模型和一个视图。创建一个名为 home_model.dart 的新文件并将以下代码移入其中。

```dart
import 'dart:async';

enum HomeViewState { Busy, DataRetrieved, NoData }

class HomeModel {

  // Controller for the stream
  final StreamController<HomeViewState> _stateController = StreamController<HomeViewState>();

  // Items that are retrieved
  List<String> listItems;

  // Stream that broadcasts the home state
  Stream<HomeViewState> get homeState => _stateController.stream;

  // Actual business logic
  Future getListData({bool hasError = false, bool hasData = true}) async {
    _stateController.add(HomeViewState.Busy);
    await Future.delayed(Duration(seconds: 2));

    if (hasError) {
     return _stateController.addError(
          'An error occurred while fetching the data. Please try again later.');
    }

    if (!hasData) {
      return _stateController.add(HomeViewState.NoData);
    }

    listItems = List<String>.generate(10, (index) => '$index title');
    _stateController.add(HomeViewState.DataRetrieved);
  }
}
```

在 home 文件中，您现在可以删除所有成员变量并将其替换为一个模型实例。然后用model.getListData 替换对_getListData 的调用，用model.homeState 替换流。有了它，一个简单应用程序的反应式“架构”。这种架构没有名称。但该应用程序是架构的。这就是我想要展示的全部内容。

如果您从中获得任何见解，请留下一些掌声，我将不胜感激。Youtube 视频下周发布，订阅将不胜感激。我将发布更多 Flutter Foundation 系列文章，因此请关注我以将它们添加到您的 Feed 中。如果你有一些你无法理解或挣扎的事情，请告诉我，我会将其添加到我想写的基金会文章列表中。

本教程到此结束，但我想提一些你可以做的事情（作为一名年轻的架构师），以开始自己的旅程，使用 Flutter 构建编写良好、易于维护的移动应用程序。无需将某些架构强加到您的应用程序上。

------

**将您的文件放在文件夹中**：通过对文件进行分组来保持整洁，不需要只是基本的深层结构。视图、视图模型、模型、服务等。

**决定一个命名约定并坚持下去**：这里我们称我们的模型为模型，但这可能与我们将用来表示我们的信息的数据模型发生冲突。您必须想出一个约定来轻松识别代码中的以下内容。

- **视图文件**（代表应用程序中一个页面/视图的文件，而不是单独的组件）
- **视图状态模型**（执行业务逻辑并提供状态的文件，视图模型是一个通用名称，BLoC 现在也被抛出，控制器也很流行或者只是模型）
- **数据模型**（表示项目中结构化数据的文件）。您必须考虑这一点，因为假设您有一个可以提交报告的应用程序。您可以将视图命名为 report.dart，当您创建模型时，您需要最简单的名称，因此您也将模型命名为 report.dart。这显然行不通。但是您可以将视图命名为 report_view.dart，并将模型保留为简单模型，反之亦然。但你必须坚持下去。其余的体系结构规则您可以在进行时建立。例如，只有模型可以通过动作（BLoC）添加到流中，没有双向绑定。或者，使用双向绑定，或者不是拥有多个模型，而是拥有一个代表我们的 App State 的模型，我们将使用操作通过流向它发送消息。

无论您做出什么决定，请确保它首先解决了您的问题。然后看看使用该代码库的长期乐趣。如果您的代码从一开始就很好地分离，您可以快速更改架构并移动您的逻辑，因此在开始时不要太担心选择完美的架构。
