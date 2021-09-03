---
title: "《在Flutter中制作指纹认证应用程序》"
subtitle: ""
date: 2020-07-17T21:33:52+08:00
lastmod: 2020-07-17T21:33:52+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

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



> 本文主要展示如何在 Flutter 中为 android 应用程序实现指纹认证系统

<!--more-->

在本教程中，我将向您展示如何在 Flutter 中为 android 应用程序实现指纹认证系统。现在许多手机都配备了指纹传感器，这使得用户登

录和本地身份验证更容易，而且比使用密码更安全。

# 设置我们的项目

在我们开始编写应用程序之前，我们需要先设置一些东西。

我们需要做的第一件事是在我们的 pubspec.yaml 文件中添加 local_auth 依赖项

所以对于我的项目，我使用了这个版本，但你可能会使用最近的版本，所以我建议你检查这个链接，看看你可以使用哪个版本： 

[pub.dev/packages/local_auth/install](https://pub.dev/packages/local_auth/install)

另外，请确保您的 SDK 版本与库版本兼容。



现在我们需要在 AndroidManifest.xml 文件中添加用户权限。

![image-20210717214224400](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210717214224400.png)



![image-20210717214246778](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210717214246778.png)

在我们的示例中，我们只会为 Android 手机实现此功能，对于 IOS 则不一样，但是您可以通过访问以下链接中的文档来了解如何执行此

操作。

## 编码

现在让我们打开 main.dart 文件并开始编写应用程序。对于布局，我刚刚添加了一个按钮，允许我们进行身份验证，并添加了 3 个文本，

它将为我们提供一些信息，它不会是什么花哨的东西。在这里我不打算给你展示布局的代码，但我会在文章的最后给你项目的完整源代

码，以便你可以查看。

现在我们开始导入重要的包，所以在Material Package之后导入这两个包

```yaml
import 'package:local_auth/local_auth.dart';
import 'package:flutter/services.dart';
```

现在我们将添加 4 个主要变量

```yaml
LocalAuthentication auth = LocalAuthentication();
  bool _canCheckBiometric;
  List<BiometricType> _availableBiometric;
  String autherized = "Not autherized";
```

让我向你解释每个人的作用



- auth 对象将为我们提供使用指纹进行身份验证所需的主要功能
- _canCheckBiometric 是一个布尔值，它会告诉我们是否有生物识别传感器
- _availableBiometric 是一个对象列表，它将为我们提供设备中可用的不同生物特征，例如指纹或 faceID
- autherized是一个字符串，它会告诉我们我们是否通过身份验证

现在我们将开始编写 3 个函数

```dart
//checking bimetrics
  //this function will check the sensors and will tell us
  // if we can use them or not
  Future<void> _checkBiometric() async{
    bool canCheckBiometric;
    try{
      canCheckBiometric = await auth.canCheckBiometrics;
    } on PlatformException catch(e){
      print(e);
    }
    if(!mounted) return;

    setState(() {
      _canCheckBiometric = canCheckBiometric;
    });
  }

  //this function will get all the available biometrics inside our device
  //it will return a list of objects, but for our example it will only
  //return the fingerprint biometric
  Future<void> _getAvailableBiometrics() async{
    List<BiometricType> availableBiometric;
    try{
      availableBiometric = await auth.getAvailableBiometrics();
    } on PlatformException catch(e){
      print(e);
    }
    if(!mounted) return;

    setState(() {
      _availableBiometric = availableBiometric;
    });
  }

  //this function will open an authentication dialog
  // and it will check if we are authenticated or not
  // so we will add the major action here like moving to another activity
  // or just display a text that will tell us that we are authenticated
  Future<void> _authenticate() async{
    bool authenticated = false;
    try{
      authenticated = await auth.authenticateWithBiometrics(
          localizedReason: "Scan your finger print to authenticate",
          useErrorDialogs: true,
        stickyAuth: false
      );
    } on PlatformException catch(e){
      print(e);
    }
    if(!mounted) return;

    setState(() {
       autherized = authenticated ? "Autherized success" : "Failed to authenticate";
    });
  }
```

请注意，如果您尚未在模拟器或设备中设置指纹安全性，则将返回对象列表的第二个函数可能会返回一个空列表，因此要修复该问题，请

打开手机设置，转到安全性并添加指纹认证，然后重新启动应用程序，您将看到检测到指纹。


现在我们已经创建了我们需要的所有函数和变量，让我们调用它们。因此，第 2 个函数将在 InitState 函数中调用，该函数将在呈现应用

程序布局之前检查生物特征，并在我们按下按钮时调用身份验证函数。

现在我们已经完成了应用程序的主要部分，让我向您展示完整的源代码。

```dart
import 'package:flutter/material.dart';
import 'package:local_auth/local_auth.dart';
import 'package:flutter/services.dart';
void main() => runApp(MaterialApp(
  
  home: AuthApp(),
));

class AuthApp extends StatefulWidget {
  @override
  _AuthAppState createState() => _AuthAppState();
}

class _AuthAppState extends State<AuthApp> {
  LocalAuthentication auth = LocalAuthentication();
  bool _canCheckBiometric;
  List<BiometricType> _availableBiometric;
  String authorized = "Not authorized";

  //checking bimetrics
  //this function will check the sensors and will tell us
  // if we can use them or not
  Future<void> _checkBiometric() async{
    bool canCheckBiometric;
    try{
      canCheckBiometric = await auth.canCheckBiometrics;
    } on PlatformException catch(e){
      print(e);
    }
    if(!mounted) return;

    setState(() {
      _canCheckBiometric = canCheckBiometric;
    });
  }

  //this function will get all the available biometrics inside our device
  //it will return a list of objects, but for our example it will only
  //return the fingerprint biometric
  Future<void> _getAvailableBiometrics() async{
    List<BiometricType> availableBiometric;
    try{
      availableBiometric = await auth.getAvailableBiometrics();
    } on PlatformException catch(e){
      print(e);
    }
    if(!mounted) return;

    setState(() {
      _availableBiometric = availableBiometric;
    });
  }

  //this function will open an authentication dialog
  // and it will check if we are authenticated or not
  // so we will add the major action here like moving to another activity
  // or just display a text that will tell us that we are authenticated
  Future<void> _authenticate() async{
    bool authenticated = false;
    try{
      authenticated = await auth.authenticateWithBiometrics(
          localizedReason: "Scan your finger print to authenticate",
          useErrorDialogs: true,
        stickyAuth: false
      );
    } on PlatformException catch(e){
      print(e);
    }
    if(!mounted) return;

    setState(() {
       authorized = authenticated ? "Autherized success" : "Failed to authenticate";
    });
  }

  @override
  void initState() {
    // TODO: implement initState
    _checkBiometric();
    _getAvailableBiometrics();
  }

  @override
  Widget build(BuildContext context) {
    return SafeArea(
      child: Scaffold(
        body: Column(
          children: <Widget>[
            Center(
              child: RaisedButton(
                onPressed: _authenticate,
                child:Text("Get Biometric"),
              ),
            ),
            Text("Can check biometric: $_canCheckBiometric"),
            Text("Available biometric: $_availableBiometric"),
            Text("Current State: $authorized"),
          ],
        ),
      ),
    );
  }
}



```

