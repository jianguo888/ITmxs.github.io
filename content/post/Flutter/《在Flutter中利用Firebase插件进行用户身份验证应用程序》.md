---
title: "《在Flutter中利用Firebase插件进行用户身份验证应用程序》"
subtitle: ""
date: 2021-07-17T23:26:57+08:00
lastmod: 2021-07-17T23:26:57+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

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



> 本文主要介绍使用 Firebase 和 Flutter 进行用户身份验证

<!--more-->



![image-20210717232730290](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210717232730290.png)

在本教程中，我将向您展示如何使用 Firebase 和 Flutter 制作一个简单的用户身份验证系统。

在这篇文章中，我不打算解释什么是 Flutter 以及如何使用它，因此您至少应该具备 Flutter 和 Dart 编程语言的基础知识。



# 火力基地？它是什么

在我们开始编码之前，让我们先谈谈 Firebase。Firebase 是由 Google 开发的用于创建移动和网络应用程序的平台。换句话说，它将帮助您更快、更轻松地构建应用程序的后端。Firebase 为开发人员提供许多服务，例如用户身份验证服务、firestore、实时数据库等等，这就是为什么如果您打算找工作或成为自由职业者，学习和掌握它是如此重要，因为许多客户都要求应用程序使用这项技术

## 设置我们的项目

首先，我邀请您通过以下链接注册 firebase： [firebase.google.com](https://firebase.google.com/)

登录帐户后，只需在主页上创建一个新项目并按照以下步骤操作

1-创建一个新项目

[![img](https://1.bp.blogspot.com/-OLDzwPg4CiI/X3N1wBs8QDI/AAAAAAAACpI/F4byNAbQLL8cKeUVQ1PHoHE-E0Nr-mUcgCLcBGAsYHQ/w640-h360/page1.PNG)

2-按照以下步骤，为您的项目命名

![img](https://1.bp.blogspot.com/-Gg6xUGH2Zsg/X3N7tPEkmjI/AAAAAAAACpU/gxVhPFl0v9MxoQlaZxfUNEf80rGDS7xtgCLcBGAsYHQ/w640-h360/page2.PNG)



3-您可以禁用 firebase 分析，因为我们不会在本教程中使用它

![img](https://1.bp.blogspot.com/-ewsbsr7KbFQ/X3N8BPteeMI/AAAAAAAACpc/6Om880_gqNwzBLW4U1VzcLOvXM2RnhZLgCLcBGAsYHQ/w640-h360/page3.PNG)



4-等待它加载，然后单击继续

![img](https://1.bp.blogspot.com/-dxSDBfIvrq4/X3N-5Q4RBlI/AAAAAAAACpo/8Cjt5nixgNQbKrSPcALTGVQCrI3dM_XeACLcBGAsYHQ/w640-h360/page4.PNG)

![img](https://1.bp.blogspot.com/-ylLNAXkL02g/X3OCRSkMfPI/AAAAAAAACp0/Y9fQzrBmOHEDP-IbDXqsCTNnULmEULZcQCLcBGAsYHQ/w640-h360/page5.PNG)



5- 现在将打开一个新窗口。单击 android 图标按钮将您的 android 应用程序添加到 firebase

![img](https://1.bp.blogspot.com/--rZy8OHE01s/X3OGVIgAElI/AAAAAAAACqA/oP2FTLp_HY4EHYuRB7MJKNM1wYpgD8WdgCLcBGAsYHQ/w640-h360/page6.PNG)



6- 为您的项目命名并按照步骤操作

![img](https://1.bp.blogspot.com/-8FqSP9LKe-0/X3ON0ckGgbI/AAAAAAAACqM/BD6GEXkd5E8XC3mhH3VnT12dRNp79FlHACLcBGAsYHQ/w640-h360/page8.PNG)



对于应用程序 ID，您将在应用程序文件夹内的 build.gradle 文件中找到它[![img](https://1.bp.blogspot.com/-CMBSH2BkcCU/X3OSs7DV1-I/AAAAAAAACqY/x7O5MYp4pNcHyCZ4EdVoBUq3at2FbsvwwCLcBGAsYHQ/w640-h460/page7.PNG)]



现在继续其他步骤并仔细阅读文档和每个文件的位置，然后单击下一步并运行您的应用程序以在您的应用程序和服务器之间建立连接。建立连接后，我们必须在 pubspec.yaml 文件中添加身份验证包并开始编码

添加[firebase auth 包](https://pub.dev/packages/firebase_auth)

在我们开始编写我们的应用程序之前，我们必须做最后一步。

打开您的 firebase 身份验证控制台，然后单击身份验证

![img](https://1.bp.blogspot.com/-8CpJdWGzyEs/X3OigwmNJ-I/AAAAAAAACqk/US7Eyi5poQ4AJDgvmLoAr62XWVTKZ_GtQCLcBGAsYHQ/w640-h360/page12.PNG)



现在点击选择登录方法并选择电子邮件和密码

![img](https://1.bp.blogspot.com/-GarYhwxaRQU/X3Oj-Vj9cSI/AAAAAAAACqw/B2lWtG17JbM0rFtuRUjalcp_fQ5ueP-PQCLcBGAsYHQ/w640-h360/page13.PNG)



![img](https://1.bp.blogspot.com/-FO2u4zS7u8s/X3OkdvD5n-I/AAAAAAAACq4/WEuE6ddYqkg29yWlvAUyur1k5g9LpJyHwCLcBGAsYHQ/w640-h360/page14.PNG)



[![img](https://1.bp.blogspot.com/-quUA7feG4Y0/X3OlJ6YDH1I/AAAAAAAACrA/gefR8TRnSCMOxalLXwjeqC2IERENboVYQCLcBGAsYHQ/w640-h360/page15.PNG)



在我们的签名方法列表的底部，您会找到一个名为“匿名登录”的方法，您可以启用它，因为我们将使用它来测试我们的应用程序。

现在设置过程已经完成，让我们开始编写我们的应用程序

```dart
import 'package:firebase_auth/firebase_auth.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:flutter/material.dart';
import 'authsystem.dart';

class LoginPage extends StatefulWidget {
  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  //Instancite the FirebaseAuth

  var _myEmailController = TextEditingController();
  var _myPasswordController = TextEditingController();

  @override
  void dispose() {
    _myEmailController.dispose();
    _myPasswordController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Login Firebase"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(24.0),
        child: Column(
          children: [
            TextField(
              controller: _myEmailController,
              decoration: InputDecoration(
                  hintText: "Email",
                  filled: true,
                  fillColor: Colors.grey[10],
                  border: InputBorder.none),
            ),
            SizedBox(
              height: 20.0,
            ),
            TextField(
              controller: _myPasswordController,
              obscureText: true,
              decoration: InputDecoration(
                  hintText: "password",
                  filled: true,
                  fillColor: Colors.grey[10],
                  border: InputBorder.none),
            ),
            SizedBox(
              height: 20.0,
            ),
            Center(
              child: RaisedButton(
                onPressed: () {
                  print("Email:${_myEmailController.text}");
                  print("Email:${_myPasswordController.text}");
                  /** Here we are going to place our sign in with email code */
                },
                child: Text(
                  "Login",
                  style: TextStyle(color: Colors.white),
                ),
                color: Colors.amber,
              ),
            ),
            Text("Or"),
            RaisedButton(
              onPressed: () {
                /** Here we are going place our Authentication System Code */
              },
              child: Text("sign in as guest"),
              color: Colors.amber,
            )
          ],
        ),
      ),
    );
  }
}

```

现在让我们返回 main.dart 文件并添加一些代码，因为您可能知道，firebase 不断更新，在 2020 年的最新更新中，我们必须先初始化我们的应用程序，然后才能使用 firebase，这就是我们添加 firebase_core 包的原因在我们的应用程序中。所以我们必须创建一个 Future 构建器，让我们更容易捕捉错误

main.dart

```dart
import 'package:flutter/material.dart';
import 'package:loginsys/authsystem.dart';
import 'package:firebase_core/firebase_core.dart';
import 'authpage.dart';

void main() {
  runApp(MaterialApp(
    home: AuthenticationExample(),
  ));
}

class AuthenticationExample extends StatefulWidget {
  @override
  _AuthenticationExampleState createState() => _AuthenticationExampleState();
}

class _AuthenticationExampleState extends State<AuthenticationExample> {
  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      future: Firebase.initializeApp(),
      builder: (context, snapshot) {
        if (snapshot.hasError) {
          return Center(
            child: Text("Someting went Worng"),
          );
        }
        if (snapshot.connectionState == ConnectionState.done) {
          return LoginPage();
        }
        return Center(
          child: CircularProgressIndicator(),
        );
      },
    );
  }
}

```

现在我们初始化了我们的应用程序，并创建了我们的布局，它是我们的主要功能，即登录功能。

## 使用 Firebase 和 Flutter 匿名登录

我们将从使用 firebase 登录的最简单方法开始，它是匿名登录或访客登录。这部分非常简单，首先，我们将创建一个 FirebaseAuth 实例，我们将使用 SignInAnonymously 方法

现在就在 intaite auth 注释之后添加这一行

```
//Instanciate the AuthService Class
  final FirebaseAuth _auth = FirebaseAuth.instance;
```

现在在我们匿名登录的 Raised 按钮中添加这个函数就在 OnPressed 方法中

 

```dart
/** Here we are going place our Anonymous Authentication System Code */
                _auth.signInAnonymously().then((UserCredential user) {
                  Navigator.of(context).push(MaterialPageRoute(
                    builder: (context) => LandingPage(),
                  ));
                });

```

最后让我们创建一个登陆页面功能，允许我们在登录时登陆

landingpage.dart

```dart
import 'package:flutter/material.dart';

class LandingPage extends StatefulWidget {
  @override
  _LandingPageState createState() => _LandingPageState();
}

class _LandingPageState extends State<LandingPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text("Welcome Sir"),
      ),
    );
  }
}

```

这就是我们的登录功能，如果您运行您的应用程序并单击以访客身份登录按钮，您将进入该页面，当您打开 Firebase 控制台时，您会发现一个新用户已添加到您的用户列表中

在下一个教程中，我们将看到如何使用电子邮件和密码登录，以及如何创建注册表和创建注册功能。