+++
title="使用 FocusScopeNode 在 TextFormFields 之间轻松移动焦点"
tags=["flutter"]
categories=["flutter"]
date="2019-12-13T21:00:00+08:00"
toc=true

+++

`Form`和`TextFormField`是在 Flutter 中输入文本时非常有用的小部件。

我们可以提供一种在键盘上按“下一步”时移动输入焦点的便捷方法吗？

使用`FocusScopeNode`，这是非常容易做到的。

假设您有一个电子邮件和密码输入表单，如下所示：

```dart
import 'package:flutter/material.dart';

class EmailPasswordSignInForm extends StatefulWidget {
  @override
  _EmailPasswordSignInFormState createState() =>
      _EmailPasswordSignInFormState();
}

class _EmailPasswordSignInFormState extends State<EmailPasswordSignInForm> {
  final FocusScopeNode _node = FocusScopeNode();
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();

  @override
  void dispose() {
    _node.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("FocusScopeNode "),
      ),
      body: Container(
        child: Form(
          key: _formKey,
          child: FocusScope(
            node: _node,
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[
                // email
                TextFormField(
                  decoration: InputDecoration(
                    labelText: 'Email',
                    hintText: 'https://luckly.work/',
                  ),
                  textInputAction: TextInputAction.next,
                  keyboardType: TextInputType.emailAddress,
                  // move to the next field
                  onEditingComplete: _node.nextFocus,
                ),
                // password
                TextFormField(
                  decoration: InputDecoration(
                    labelText: 'Password',
                  ),
                  obscureText: true,
                  textInputAction: TextInputAction.done,
                  // move to the next field
                  onEditingComplete: _node.nextFocus,
                ),
                // submit
                RaisedButton(
                  child: Text('Sign In'),
                  onPressed: () {/* submit code here */},
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}

```

