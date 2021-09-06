---
title: "Markdown"
date: 2021-09-06T11:08:42+08:00
draft: true
---



https://www.shuzhiduo.com/A/kvJ3wWZwJg/

##### 安装依赖

```dart
dependencies:
  flutter:
    sdk: flutter
  flutter_markdown: ^0.3.4
```

保存后，编辑器自动下载依赖，或者执行 *flutter pub get*

创建markdown文件,放开pubspec.yaml 静态文件路径

```dart
 assets:
    - assets/markdown.md
```

加载本地markdown.md文件。

> main.dart文件

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:flutter_markdown/flutter_markdown.dart';void main() => runApp(MyApp());class MyApp extends StatelessWidget {
  const MyApp({Key key}) : super(key: key);  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        appBar: AppBar(
          title: Text('Flutter Markdown Demo'),
          centerTitle: true,
        ),
        body: FlutterMarkdown(),
      ),
    );
  }
}class FlutterMarkdown extends StatelessWidget {
  const FlutterMarkdown({Key key}) : super(key: key);  @override
  Widget build(BuildContext context) {
    return Container(
      child: FutureBuilder(
        future: rootBundle.loadString('assets/markdown.md'),
        builder: (BuildContext context,AsyncSnapshot snapshot){
          if(snapshot.hasData){
            return Markdown(data: snapshot.data);
          }else{
            return Center(
              child: Text("加载中..."),
            );
          }
        },
      ),
    );
  }
}
```

展示markdown效果

![img](https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHBzL2kubG9saS5uZXQvMjAyMC8wMy8wOS94UU1lb1dpSGRETzYyMWsucG5n.jpg)

Markdown 组件有两种**Markdown(data: markdownSource)** 和 **MarkdownBody(data: markdownSource)**

> Markdown() 返回 ListView widget ，并且有默认padding：const EdgeInsets.all(16.0)

> MarkdownBody() 返回 Column widget 没有默认的padding并且不支持滚动

**Markdown支持自定义样式,监听点击超链**

```dart
Markdown(
    data: snapshot.data,
    styleSheet: MarkdownStyleSheet(
        // 支持修改样式
        h1: TextStyle(fontSize: 14),
    ),
    onTapLink: (url){
        // 获取点击链接，可以使用webview展示
        print(url);
    },
)
```

**Markdown源码**

## 使用

使用 Markdown 很简单，只需将源 markdown 作为字符串传递，参数如下：

```dart
Markdown({
    Key key,
    //填充内容
    @required String data,
    bool selectable = false,
    //定制 Markdown 样式
    MarkdownStyleSheet styleSheet,
    MarkdownStyleSheetBaseTheme styleSheetTheme,
    //设置代码高亮
    SyntaxHighlighter syntaxHighlighter,
    //获取点击链接，可以使用 webview 展示
    MarkdownTapLinkCallback onTapLink,
    String imageDirectory,
    md.ExtensionSet extensionSet,
    MarkdownImageBuilder imageBuilder,
    MarkdownCheckboxBuilder checkboxBuilder,
    Map<String, MarkdownElementBuilder> builders = const {},
    this.padding = const EdgeInsets.all(16.0),
    this.controller,
    this.physics,
    this.shrinkWrap = false,
  })
```

syntaxHighlighter 可以设置代码高亮，这里有个坑，需要结合插件 syntax_highlighter 一起使用，后面再说。

```dart
class Markdown extends MarkdownWidget {
  /// Creates a scrolling widget that parses and displays Markdown.
  const Markdown({
    Key key,
    @required String data,
    bool selectable = false,
    MarkdownStyleSheet styleSheet,
    MarkdownStyleSheetBaseTheme styleSheetTheme,
    SyntaxHighlighter syntaxHighlighter,
    MarkdownTapLinkCallback onTapLink,
    String imageDirectory,
    md.ExtensionSet extensionSet,
    MarkdownImageBuilder imageBuilder,
    MarkdownCheckboxBuilder checkboxBuilder,
    this.padding = const EdgeInsets.all(16.0),
    this.controller,
    this.physics,
    this.shrinkWrap = false,
  }) : super(
          key: key,
          data: data,
          selectable: selectable,
          styleSheet: styleSheet,
          styleSheetTheme: styleSheetTheme,
          syntaxHighlighter: syntaxHighlighter,
          onTapLink: onTapLink,
          imageDirectory: imageDirectory,
          extensionSet: extensionSet,
          imageBuilder: imageBuilder,
          checkboxBuilder: checkboxBuilder,
        );
}
```

**意外发现**

在创建项目之初，随手就创建了 *flutter create flutter_markdown* ，然后安装flutter_markdown依赖失败，考虑到project name 和 flutter_markdown重名了，那就换一个项目名称 *flutter create markdown* 然后flutter pub get 然后又报错了，flutter_markdown依赖另外一个markdown package。好吧，*flutter create flutter_md_demo* 一切正常了。

**Flutter 项目名称不能和package依赖包名相同**

代码高亮



https://jishuin.proginn.com/p/763bfbd2e926





博客

超链接

对于插件[flutter_markdown](https://pub.dev/packages/flutter_markdown)你可以这样做

```dart
import 'package:flutter_markdown/flutter_markdown.dart';
import 'package:url_launcher/url_launcher.dart';

final String _markdownData = "-This is [Google link](https://www.google.com)";

// view
MarkdownBody(
    data: _markdownData,
    onTapLink: (text, url, title){
        launch(url);
    },
)
```

**更新**flutter_markdown 0.5.1 或更低版本：

```dart
MarkdownBody(
    data: _markdownData,
    onTapLink: (url) {
        launch(url);
    },
)
```







```
Material(
      color: Colors.orange,
      child: Container(
        child: FutureBuilder(
          future: rootBundle.loadString('assets/markdown/markdown.md'),
          builder: (BuildContext context, AsyncSnapshot snapshot) {
            if (snapshot.hasData) {
              return Markdown(
                controller: ScrollController(),
                selectable: true,
                data: snapshot.data,
                syntaxHighlighter: new HighLight(),
                //代码高亮
                styleSheet: MarkdownStyleSheet(
                  // 支持修改样式
                  h1: TextStyle(fontSize: 14), p: new TextStyle(fontSize: 16),
                  h2: new TextStyle(color: Colors.blue, fontSize: 24),
                ),
                onTapLink: (text, url, title) {
                  launch(url);
                },
              );
            } else {
              return Center(
                child: Text("加载中..."),
              );
            }
          },
        ),
      ),
    );
```

