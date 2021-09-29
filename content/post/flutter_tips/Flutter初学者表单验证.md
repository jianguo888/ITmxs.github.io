---
title: "Flutter初学者表单验证"
date: 2021-09-29T09:17:35+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

验证表单是所有数字交互中的常见做法，移动应用程序也不例外。今天我们将构建一个非常简单的表单验证来向您展示这是如何在 Flutter 中完成的。

## 概述

Flutter 为我们提供了一个 Form 小部件。您可以根据需要构建小部件的子部件，只需添加 TextFormField 小部件即可将其与表单分开。Form 子小部件中的任何 TextFormField 都将成为您可以使用的 Form 中的字段。每个表单都必须有一个 FormState 类型的键，允许您随时访问状态。

## 执行

我们将制作一个包含姓名、号码和电子邮件的表单，我们将验证所有这些。我们将从一个基本项目开始，然后在主应用程序下添加一个有状态的 HomeView。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
       home: HomeView());
  }
}

class HomeView extends StatefulWidget {
  @override
  _HomeViewState createState() => _HomeViewState();
}

class _HomeViewState extends State<HomeView> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container()
    );
  }
}
```

对于脚手架的主体，我们将添加一个表单，并在表单中添加一个空的列。我们还将为表单提供一个密钥，我们可以使用它来检查状态并要求表单保存。

```dart
class _HomeViewState extends State<HomeView> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Form(
        key: _formKey,
        child: Column(children: <Widget>[

        ]),
      )
    );
  }
}
```

这就是所有设置完成。让我们添加我们的第一个字段。我们将从名称开始。在 Column 添加一个 TextFormField 小部件，给它一个键盘类型的文本和一个验证功能。对于验证，我们将只检查名称是否少于 2 个字符，如果是，则显示验证错误。在验证器函数中，当您返回一个字符串时，它会将其作为验证错误消息。如果你什么都不返回，那么就不会有错误。

```dart
...
 Widget build(BuildContext context) {
    return Scaffold(
      body: Form(
        key: _formKey,
        child: Column(children: <Widget>[
          TextFormField(
            decoration: const InputDecoration(labelText: 'Name'),
            keyboardType: TextInputType.text,
            validator: (value) {
              if(value.length < 2){
                return 'Name not long enough';
              }
            },
          ),
        ]),
      )
    );
  }
...
```

如果您在该字段中输入，您将不会看到任何验证消息出现。这是因为表单不会自动验证。要启用此功能，请将封闭的 Form 小部件的 autovalidate 值设置为 true。

```dart
Form(
  autovalidate: true,
  key: _formKey,
  child: Column(children: <Widget>[ ... ]),
);
```

启用此功能后，您现在每次键入字符时都会在所有字段上进行验证。接下来我们将在用户准备好时获取并保存表单数据。添加一个 FloatingActionButton 来使用键检查表单是否有效，如果有效则调用 save 。

```dart
...
return Scaffold(
 floatingActionButton: FloatingActionButton(
  onPressed: () {
      if(_formKey.currentState.validate()) {
        _formKey.currentState.save();
      }
    },
  ),
...
);
```

当在 currentState 上调用此函数时，它会在所有名为 onSaved 的 TextFormField 上触发回调。此函数返回字段中的当前文本。我们将使用它来将我们的数据保存到一个变量中，然后我们可以在 save 调用之后使用它。将 onSaved 回调添加到您的 TextFormField。

```dart
// Add name variable to the class
String _name;

...
 TextFormField(
    decoration: const InputDecoration(labelText: 'Name'),
    ...
    onSaved: (value)  => _name = value,
 );
```

这就是构建文本表单所需的一切。现在我们可以复制和更新其他字段的验证。让我们接下来做电子邮件。

```dart
 TextFormField(
    decoration: const InputDecoration(labelText: 'Email'),
    keyboardType: TextInputType.emailAddress,
    validator: (value) {
      if (!EmailValidator.validate(value)) {
        return 'Please enter a valid email';
      }
    },
  ),
```

验证将使用 EmailValidator 包完成，因此将其添加到您的 pubspec 中。

```yaml
email_validator: ^1.0.0
```

然后最后我们可以添加我们的电话字段。

```dart
TextFormField(
    decoration: const InputDecoration(labelText: 'Mobile'),
    keyboardType: TextInputType.phone,
    validator: (value) {
      var potentialNumber = int.tryParse(value);
      if (potentialNumber == null) {
        return 'Enter a phone number';
      }
    },
  ),
```

我们将检查它是否是一个数字，如果不是，我们将显示消息。确保将键盘类型设置为电话。

这就是表单验证。您所要做的就是为 onSave 函数上的每个字段设置值，例如使用`_name`.