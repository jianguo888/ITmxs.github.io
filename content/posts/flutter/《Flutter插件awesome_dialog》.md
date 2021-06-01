---
title: "《Flutter插件awesome_dialog》"
subtitle: ""
date: 2021-05-24T22:10:25+08:00
lastmod: 2021-05-24T22:10:25+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","插件"]
categories: ["flutter","插件"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍一个新的Flutter软件包项目，用于简单而出色的对话框，我们在应用程序中使用它来向用户显示某些警告或确认，在这种情况下，我们可以通过使用一些属性在其中使用其他类型的动画。并可以设置颜色，文字等

<!--more-->



### 使用

**步骤1：添加依赖项。**

> ***将依赖项添加到pubspec-yaml文件。\***

```
dependencies:   
awesome_dialog: ^1.3.2
```

> ***步骤2：导入软件包：\***

```
import 'package:awesome_dialog/awesome_dialog.dart';
```

> ***步骤3：运行flutter包\***



https://github.com/marcos930807/awesomeDialogs/raw/master/doc/gif.gif



### 属性 ：

> ***以下是“awesome_dialog”对话框的基本属性。\***

- **animType-**使用animType属性更改对话框的动画。
- **dialogType** — dialogType用于对话框类型属性，例如info，error等。
- **title-** title属性用于将对话框标题设置为对话框。
- **desc** — desc属性用于对话框说明。
- **autoHide** — autoHide属性会在一段时间后隐藏，可以将其时间设置为任何时间。

### 例子

```
AwesomeDialog(
            context: context,
            dialogType: DialogType.INFO,
            animType: AnimType.BOTTOMSLIDE,
            title: 'Dialog Title',
            desc: 'Dialog description here.............',
            btnCancelOnPress: () {},
            btnOkOnPress: () {},
            )..show();
```

### Custom Body

如果指定了正文，则标题和描述将被忽略，这允许进一步自定义对话框。

```
AwesomeDialog(
            context: context,
            animType: AnimType.SCALE,
            dialogType: DialogType.INFO,
            body: Center(child: Text(
                    'If the body is specified, then title and description will be ignored, this allows to further customize the dialogue.',
                    style: TextStyle(fontStyle: FontStyle.italic),
                  ),),
            title: 'This is Ignored',
            desc:   'This is also Ignored',
            btnOkOnPress: () {},
                 )..show();
```

### 自定义按钮

要使用自定义按钮，只需指定buttonOk或btnCancel小部件，所有文本参数图标等都将被忽略。



#### demo

```dart
import 'package:awesome_dialog/awesome_dialog.dart';
import 'package:flutter/material.dart';

import 'routes.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Fancy Dialog Example',
      theme: ThemeData.dark(),
      initialRoute: '/',
      onGenerateRoute: RouteGenerator.generateRoute,
    );
  }
}

class HomePage extends StatefulWidget {
  const HomePage({
    Key? key,
  }) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text('Awesome Dialog Example'),
        ),
        body: Center(
            child: Container(
          padding: EdgeInsets.all(16),
          child: SingleChildScrollView(
            child: Column(
              children: <Widget>[
                AnimatedButton(
                  text: 'Info Dialog fixed width and sqare buttons',
                  pressEvent: () {
                    AwesomeDialog(
                      context: context,
                      borderSide: BorderSide(color: Colors.green, width: 2),
                      width: 280,
                      buttonsBorderRadius: BorderRadius.all(Radius.circular(2)),
                      headerAnimationLoop: false,
                      animType: AnimType.BOTTOMSLIDE,
                      title: 'INFO',
                      desc: 'Dialog description here...',
                      showCloseIcon: true,
                      btnCancelOnPress: () {},
                      btnOkOnPress: () {},
                    )..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Question Dialog With Custom BTN Style',
                  pressEvent: () {
                    AwesomeDialog(
                      context: context,
                      dialogType: DialogType.QUESTION,
                      headerAnimationLoop: false,
                      animType: AnimType.BOTTOMSLIDE,
                      title: 'Question',
                      desc: 'Dialog description here...',
                      buttonsTextStyle: TextStyle(color: Colors.black),
                      showCloseIcon: true,
                      btnCancelOnPress: () {},
                      btnOkOnPress: () {},
                    )..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Info Dialog Without buttons',
                  pressEvent: () {
                    AwesomeDialog(
                      context: context,
                      headerAnimationLoop: true,
                      animType: AnimType.BOTTOMSLIDE,
                      title: 'INFO',
                      desc:
                          'Lorem ipsum dolor sit amet consectetur adipiscing elit eget ornare tempus, vestibulum sagittis rhoncus felis hendrerit lectus ultricies duis vel, id morbi cum ultrices tellus metus dis ut donec. Ut sagittis viverra venenatis eget euismod faucibus odio ligula phasellus,',
                    )..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Warning Dialog',
                  color: Colors.orange,
                  pressEvent: () {
                    AwesomeDialog(
                        context: context,
                        dialogType: DialogType.WARNING,
                        headerAnimationLoop: false,
                        animType: AnimType.TOPSLIDE,
                        showCloseIcon: true,
                        closeIcon: Icon(Icons.close_fullscreen_outlined),
                        title: 'Warning',
                        desc:
                            'Dialog description here..................................................',
                        btnCancelOnPress: () {},
                        btnOkOnPress: () {})
                      ..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Error Dialog',
                  color: Colors.red,
                  pressEvent: () {
                    AwesomeDialog(
                        context: context,
                        dialogType: DialogType.ERROR,
                        animType: AnimType.RIGHSLIDE,
                        headerAnimationLoop: false,
                        title: 'Error',
                        desc:
                            'Dialog description here..................................................',
                        btnOkOnPress: () {},
                        btnOkIcon: Icons.cancel,
                        btnOkColor: Colors.red)
                      ..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Succes Dialog',
                  color: Colors.green,
                  pressEvent: () {
                    AwesomeDialog(
                        context: context,
                        animType: AnimType.LEFTSLIDE,
                        headerAnimationLoop: false,
                        dialogType: DialogType.SUCCES,
                        title: 'Succes',
                        desc:
                            'Dialog description here..................................................',
                        btnOkOnPress: () {
                          debugPrint('OnClcik');
                        },
                        btnOkIcon: Icons.check_circle,
                        onDissmissCallback: () {
                          debugPrint('Dialog Dissmiss from callback');
                        })
                      ..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'No Header Dialog',
                  color: Colors.cyan,
                  pressEvent: () {
                    AwesomeDialog(
                      context: context,
                      headerAnimationLoop: false,
                      dialogType: DialogType.NO_HEADER,
                      title: 'No Header',
                      desc:
                          'Dialog description here..................................................',
                      btnOkOnPress: () {
                        debugPrint('OnClcik');
                      },
                      btnOkIcon: Icons.check_circle,
                    )..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Custom Body Dialog',
                  color: Colors.blueGrey,
                  pressEvent: () {
                    AwesomeDialog(
                      context: context,
                      animType: AnimType.SCALE,
                      dialogType: DialogType.INFO,
                      body: Center(
                        child: Text(
                          'If the body is specified, then title and description will be ignored, this allows to further customize the dialogue.',
                          style: TextStyle(fontStyle: FontStyle.italic),
                        ),
                      ),
                      title: 'This is Ignored',
                      desc: 'This is also Ignored',
                    )..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Auto Hide Dialog',
                  color: Colors.purple,
                  pressEvent: () {
                    AwesomeDialog(
                      context: context,
                      dialogType: DialogType.INFO,
                      animType: AnimType.SCALE,
                      title: 'Auto Hide Dialog',
                      desc: 'AutoHide after 2 seconds',
                      autoHide: Duration(seconds: 2),
                    )..show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Testing Dialog',
                  color: Colors.orange,
                  pressEvent: () {
                    AwesomeDialog(
                      context: context,
                      keyboardAware: true,
                      dismissOnBackKeyPress: false,
                      dialogType: DialogType.WARNING,
                      animType: AnimType.BOTTOMSLIDE,
                      btnCancelText: "Cancel Order",
                      btnOkText: "Yes, I will pay",
                      title: 'Continue to pay?',
                      padding: const EdgeInsets.all(16.0),
                      desc:
                          'Please confirm that you will pay 3000 INR within 30 mins. Creating orders without paying will create penalty charges, and your account may be disabled.',
                      btnCancelOnPress: () {},
                      btnOkOnPress: () {},
                    ).show();
                  },
                ),
                SizedBox(
                  height: 16,
                ),
                AnimatedButton(
                  text: 'Body with Input',
                  color: Colors.blueGrey,
                  pressEvent: () {
                    late AwesomeDialog dialog;
                    dialog = AwesomeDialog(
                      context: context,
                      animType: AnimType.SCALE,
                      dialogType: DialogType.INFO,
                      keyboardAware: true,
                      body: Padding(
                        padding: const EdgeInsets.all(8.0),
                        child: Column(
                          children: <Widget>[
                            Text(
                              'Form Data',
                              style: Theme.of(context).textTheme.headline6,
                            ),
                            SizedBox(
                              height: 10,
                            ),
                            Material(
                              elevation: 0,
                              color: Colors.blueGrey.withAlpha(40),
                              child: TextFormField(
                                autofocus: true,
                                minLines: 1,
                                decoration: InputDecoration(
                                  border: InputBorder.none,
                                  labelText: 'Title',
                                  prefixIcon: Icon(Icons.text_fields),
                                ),
                              ),
                            ),
                            SizedBox(
                              height: 10,
                            ),
                            Material(
                              elevation: 0,
                              color: Colors.blueGrey.withAlpha(40),
                              child: TextFormField(
                                autofocus: true,
                                keyboardType: TextInputType.multiline,
                                maxLengthEnforced: true,
                                minLines: 2,
                                maxLines: null,
                                decoration: InputDecoration(
                                  border: InputBorder.none,
                                  labelText: 'Description',
                                  prefixIcon: Icon(Icons.text_fields),
                                ),
                              ),
                            ),
                            SizedBox(
                              height: 10,
                            ),
                            AnimatedButton(
                                text: 'Close',
                                pressEvent: () {
                                  dialog.dissmiss();
                                })
                          ],
                        ),
                      ),
                    )..show();
                  },
                ),
              ],
            ),
          ),
        )));
  }
}
```

您可以根据自己的需要进行修改和试验