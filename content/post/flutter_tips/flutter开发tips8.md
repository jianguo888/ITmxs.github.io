---
title: "flutter开发tips8"
date: 2021-08-18T14:05:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---



# Shimmer



如果你想创造一个很酷的**微光效果**，你可以很容易地使用[Shimmer包](https://pub.dev/packages/shimmer)。这可以在加载某些内容时很好地使用，因此用户可以看到正在发生的事情。

```dart
//pubspec.yaml
dependencies:
  shimmer: ^2.0.0
//your_file.dart
import 'package:shimmer/shimmer.dart';
SizedBox(
  width: 200.0,
  height: 100.0,
  child: Shimmer.fromColors(
    baseColor: Colors.red,
    highlightColor: Colors.yellow,
    child: Text(
      'Shimmer',
      textAlign: TextAlign.center,
      style: TextStyle(
        fontSize: 40.0,
        fontWeight:
        FontWeight.bold,
      ),
    ),
  ),
)
```

# Google Fonts





```dart
//pubspec.yaml
dependencies:
  google_fonts: ^2.1.0

//you_file.dart
import 'package:google_fonts/google_fonts.dart';
Text('Google fonts', style: GoogleFonts.pacific),
```

# Scaffold gradient background

包[scaffold_gradient_background](https://pub.dev/packages/scaffold_gradient_background)是我自己创建的一个非常新的包。

我没有将它包含在此列表中，因为它是由我创建的，而是因为我发现它**非常方便**并在我的所有项目中使用它。

使用此包，您可以轻松地将**渐变**设置**为脚手架的背景**。



```dart
//pubspec.yaml
dependencies:
  scaffold_gradient_background: ^1.0.3
//your_file.dart
import 'package:scaffold_gradient_background/scaffold_gradient_background.dart';
GradientScaffold(
  gradient: LinearGradient(
    begin: Alignment.bottomLeft,
    end: Alignment.topRight,
    colors: [
      Color(0xFF8EC5FC),
      Color(0xFFE0C3FC),
    ],
  ),
  appBar: AppBar(
   title: Text('Linear Gradient Example'),
  ),
  body: Center(
  child: Text(
    'Hello ^^',
    style: TextStyle(
      color: Colors.white,
      fontSize: 30,
    ),
    ),
  ),
);
```

# Introduction screen



如果你想有一个很酷的介绍屏幕，你可以使用包[Introduction_screen](https://pub.dev/packages/introduction_screen)。

```dart
//pubspec.yaml
dependencies:
  introduction_screen: ^2.1.0
//your_file.dart
import 'package:introduction_screen/introduction_screen.dart';
IntroductionScreen(
  pages: listPagesViewModel,
  done: const Text("Done", style: TextStyle(fontWeight: FontWeight.w600)),
  onDone: () {
    // When done button is press
  },
),

```

# Theming



一个**一贯的设计**是在任何应用程序的重要。为此，您可以在 MaterialApp 中创建一个主题。在那里您可以指定原色、文本颜色等等。

```dart
MaterialApp(
  title: appName,
  theme: ThemeData(
    // Define the default brightness and colors.
    brightness: Brightness.dark,
    primaryColor: Colors.lightBlue[800],
    accentColor: Colors.cyan[600],
    // Define the default font family.
    fontFamily: 'Georgia',
    // Define the default TextTheme. Use this to specify the default
    // text styling for headlines, titles, bodies of text, and more.
    textTheme: TextTheme(
      headline1: TextStyle(fontSize: 72.0, fontWeight: FontWeight.bold),
      headline6: TextStyle(fontSize: 36.0, fontStyle: FontStyle.italic),
      bodyText2: TextStyle(fontSize: 14.0, fontFamily: 'Hind'),
    ),
  ),
  home: MyHomePage(
    title: appName,
  ),
);
```

# Cupertinowidgets



Flutter 是为创建适用于 Android 和 iOS 的应用程序而设计的。为此，也有 Cupertinowidgets，**与 iOS 的风格**完美契合。此外，您还可以查询用户当前使用的是 iOS 还是 Android 设备，并基于此显示您选择的小部件。

```dart
Platform.isAndroid 
  ? CircularProgressIndicator() 
  : CupertinoActivityIndicator()
```

