---
title: "Flutter应用国际化实现"
date: 2021-08-23T12:58:23+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]
---



## 2.初始化项目

1. 在`pubspec.yaml`中添加`flutter_localizations`依赖并执行`packages get`

   ```
   # 国际化
   flutter_localizations:
       sdk: flutter
   ```



在androidstudio上安装插件

## 使用IDE插件

在之前有一个比较好用的Android Studio的插件：Flutter i18n

- 但是这个插件已经很久不再维护了，所以不再推荐给大家使用

目前我们可以使用另外一个插件：Flutter Intl

- 该插件更新维护频率很高，并且广受好评；
- 另外，在Android Studio和VSCode中都是支持的

我们这里以Android Studio为例，讲解其使用过程：

### 3.3.1. 安装插件

在Android Studio的Plugins中安装插件：

### 3.3.2. 初始化intl

选择工具栏Tools - Flutter Intl - **Initialize for the Project**

初始化intl

完成上面的操作之后会自动生成如下文件目录：

- generated是自动生成的dart代码
- I10n是对应的arb文件目录

### 3.3.3. 使用intl

在localizationsDelegates中配置生成的class，名字是S

- 1.添加对应的delegate
- 2.supportedLocales使用S.delegate.supportedLocales







```dart
  localizationsDelegates: [
          S.delegate, // 应用程序的翻译回调

          // AppLocalizations.delegate, // 自定义国际化内容，由intl生成
          GlobalMaterialLocalizations.delegate, // 全局Material国际化
          GlobalWidgetsLocalizations.delegate, // 全局Widget国际化
          GlobalCupertinoLocalizations.delegate, // 全局Cupertino国际化
        ],
        supportedLocales: S.delegate.supportedLocales,
```





```dart
 Center(
              child: Text(S.of(context).randomPassword),
            ),
            ButtonBar(
              alignment: MainAxisAlignment.center,
              children: [
                MaterialButton(
                  onPressed: () {
                    setState(() {
                      S.load(Locale("zh", "ZH"));
                    });
                  },
                  child: Text("中文"),
                ),
                MaterialButton(
                  onPressed: () {
                    setState(() {
                      S.load(Locale("en", "US"));
                    });
                  },
                  child: Text("英语"),
                ),
              ],
            ),
```

