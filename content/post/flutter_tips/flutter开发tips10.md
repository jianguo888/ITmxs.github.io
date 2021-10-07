---
title: "Flutter开发tips10"
date: 2021-10-05T19:27:06+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---



这是一个特殊的部分，因为我们不仅要查看包或其他东西，还要查看 VSCode 扩展、GitHub 存储库（当然还有一些包）。所以，和第一部分完全一样（我们又有点怀旧了 xD）。

那么，让我们开始吧！

# 很棒的 Flutter 片段

这个扩展在这里亲v IDE你有很多非常的**有用扑片段**。正好有 40 个带有 initState 或 dispose 等函数的片段，还有像 Listview.builder (listViewB)、GridView.count (gridViewC) 或 AnimatedBuilder (animatedBldr) 这样的小部件。

要查看完整列表，您可以阅读此[扩展](https://marketplace.visualstudio.com/items?itemName=Nash.awesome-flutter-snippets)的概述。

# Snippets 

好吧，Flutter Snippets 非常酷，但由于我们实际上是在 Dart 中编写整个程序，因此我们还需要**Dart Snippets**。为此，有一个相当不为人知的扩展[Fast Dart](https://marketplace.visualstudio.com/items?itemName=YashPaneliya.fast-dart)。您可以在那里找到 20 个片段，其中包括 tc（try-catch 块），即（If-Else）或仅 cls（Class）。

# flutter-color

这个[扩展](https://marketplace.visualstudio.com/items?itemName=circlecodesolution.ccs-flutter-color)非常有用，我每天使用它几次。它会在您的代码中检测您输入颜色代码的位置，然后在其左侧向您显示该颜色的外观。但这还不是全部。如果然后单击该框，您将获得一个**颜色选择器**，您可以使用它选择一种颜色，然后自动将其写入代码中。



![image-20211005200520842](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211005200520842.png)

# Flutter 提示和技巧

这个[存储库](https://github.com/vandadnp/flutter-tips-and-tricks)是**数百个 Flutter Tips**的集合，它们都有解释。您将在那里学习许多新的小部件，或者您将学习如何检查网站是否正常运行。

# Flutter-Course-Resource

GitHub 上的另一个[存储库](https://github.com/londonappbrewery/Flutter-Course-Resources)将教您在**Flutter 中****需要学习的**所有重要内容。您将在那里构建大量应用程序，了解 StateManagement 等等。

强烈推荐，我一定会自己使用这个存储库来继续学习。

# paddinger

每个人都知道。这个小部件需要一个**padding**，还有这个，还有这个。一直这样下去，代码看起来不漂亮。但是如果有一个包，你定义了一次填充，然后你用一个小部件包装你的孩子而不用每次都写 EdgeInsets.all(8.0) 呢？

嗯，这是存在的，它被称为[paddinger](https://pub.dev/packages/paddinger)。设置有点复杂，但是如果您查看文档，它就像任何其他软件包一样简单。

```dart
Padding(
  padding: const EdgeInsets.all(PADDING_NORMAL),
  child: Text(
    'MyText',
  ),
),
```



```dart
NormalAllPadding(
  child: Text(
    'MyText',
  ),
)
```



# Flutter 自动表单

好吧，Flutter 的范式系统，老实说，非常复杂。您必须编写大量样板代码，而对我而言，这始终是我喜欢推迟的任务。但是，自从我开始使用[flutter_auto_form](https://pub.dev/packages/flutter_auto_form)以来，情况发生了变化。这个包是一个超级**易于使用的表单系统**，没有控制器或任何东西。很棒的是，它使您可以自动跳转到下一个字段，而无需专门调用该字段。

```dart
import 'package:flutter_auto_form/flutter_auto_form.dart';

class LoginForm extends TemplateForm {
  @override
  final List<Field> fields = [
    AFTextField(
      id: 'identifier',
      name: 'identifier',
      validators: [
        MinimumStringLengthValidator(
          5,
              (e) => 'Min 5 characters, currently ${e?.length ?? 0} ',
        )
      ],
      type: AFTextFieldType.USERNAME,
    ),
    AFTextField(
      id: 'password',
      name: 'password',
      validators: [
        MinimumStringLengthValidator(
          6,
              (e) => 'Min 6 characters, currently ${e?.length ?? 0} ',
        )
      ],
      type: AFTextFieldType.PASSWORD,
    ),
  ];
}
```

现在你可以像这样在你的小部件树中使用整个东西：

```dart
AFWidget<LoginForm>(
  formBuilder: () => LoginForm(),
  submitButton: (Function({bool showLoadingDialog}) submit) => Padding(
    padding: const EdgeInsets.only(top: 32),
    child: MaterialButton(
      child: Text('Submit'),
      onPressed: () => submit(showLoadingDialog: true),
    ),
  ),
  onSubmitted: (LoginForm form) {
    // do whatever you want when the form is submitted
    print(form.toMap());
  },
);
```

现在它看起来像这样：

![image-20211005200656373](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211005200656373.png)

# 简单的动画

动画是许多人讨厌实现的东西，因为创建它们非常复杂。但幸运的是，有[simple_animations](https://pub.dev/packages/simple_animations)包，它允许您非常轻松地实现**多种动画选择，**您甚至可以创建自己的动画。您只需要了解一次包，就可以创建最好的动画。

这是一个非常简单的示例，但您可以使用该包创建更多内容：



![image-20211005200713634](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211005200713634.png)

# Supabase 

现在，这个提示是一个非常私人的提示，所以一个人可能喜欢它，另一个人可能不喜欢。但我个人很喜欢。

Supabase 是**Firebase**的**开源替代品**，您可以使用它拥有数据库、SQL、身份验证等功能。对于身份验证，它甚至提供了比 Firebase 更多的外部 OAuth 提供程序。

其他缺点是它没有分析或函数之类的东西，但为此你可以在你自己的服务器上使用 Docker 运行 Supabase，例如，不允许无故删除你的项目。

我个人在许多小应用程序中使用它，但在我还需要函数的应用程序中，不幸的是我不得不求助于更复杂的提供程序 Firebase（复杂是指 Supabase 在 Flutter 代码中更容易实现）。

# fluttertoast

本系列的最后一个技巧是关于祝酒🍞。

好吧，那当然只是个玩笑。[fluttertoast](http://pub.dev/packages/fluttertoast)是一个**toast 库**，支持两种类型的 toast 消息。一种需要 BuildContext，一种不需要。

我们将看看下面不需要 BuildContext 的那个：

```dart
Fluttertoast.showToast(
  msg: "This is Center Short Toast",
  toastLength: Toast.LENGTH_SHORT,
  gravity: ToastGravity.CENTER,
  timeInSecForIosWeb: 1,
  backgroundColor: Colors.red,
  textColor: Colors.white,
  fontSize: 16.0
);
```

现在这是一个非常简单的例子。您还可以创建自定义 Toast，可能如下所示：





![image-20211005200820685](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211005200820685.png)

# 结论

现在这个系列的最后一部分结束了，我很伤心。