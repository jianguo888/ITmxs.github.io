---
title: "《在Flutter和androidStudio中制作登录表单》"
subtitle: ""
date: 2021-07-16T20:26:48+08:00
lastmod: 2021-07-16T20:26:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","登陆表单"]
categories: ["flutter","登陆表单"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/f77be1f477e980732d57475d3ddb69d.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/f77be1f477e980732d57475d3ddb69d.png"

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



> 在本教程中，我将向您展示如何在 Flutter 和 Android studio 中制作漂亮的登录表单，
> 您将学习如何制作背景图像、如何在 Flutter中制作圆形按钮、如何添加一些不透明度以及如何添加图标到您的文本字段。

<!--more-->

```dart
import 'dart:ffi';

import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(
  home: LoginApp(),
));


class LoginApp extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Stack(
        children: <Widget>[
          Container(
            decoration: new BoxDecoration(
              image: new DecorationImage(
                  image: NetworkImage('https://images.unsplash.com/photo-1439853949127-fa647821eba0?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=634&q=80'),
              fit: BoxFit.fill
              ),
            ),
          ),
          Opacity(
            opacity: 0.5,
            child: Container(
              decoration: new BoxDecoration(
                gradient: LinearGradient(
                  begin: Alignment.topRight,
                  end: Alignment.bottomLeft,
                  colors: [Colors.blue,Colors.purpleAccent],
                ),
              ),
            ),
          ),
          Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                Text('Login',style: TextStyle(
                  fontSize: 60.0,
                  letterSpacing: 2.0,
                  color: Colors.white,
                  fontStyle: FontStyle.italic,
                ),
                ),
                Padding(
                  padding: EdgeInsets.symmetric(horizontal: 35.0, vertical: 22.0),
                  child: TextField(
                    decoration: InputDecoration(
                      filled: true,
                      fillColor: Colors.white,
                      border: InputBorder.none,
                      hintText: 'User Email',
                      contentPadding: EdgeInsets.all(16.0),
                      prefixIcon: Icon(Icons.email,color: Colors.blue,)
                    ),
                  keyboardType: TextInputType.emailAddress,
                  ),
                ),
                Padding(
                  padding: EdgeInsets.symmetric(horizontal: 35.0, vertical: 12.0),
                  child: TextField(
                    decoration: InputDecoration(
                      filled: true,
                      fillColor: Colors.white,
                      border: InputBorder.none,
                      hintText: 'User Password',
                      contentPadding: EdgeInsets.all(16.0),
                      prefixIcon: Icon(Icons.lock,color: Colors.blue,)
                    ),
                    obscureText: true,
                  ),
                ),
                Text("Forget your password ?", style: TextStyle(color: Colors.white),),
                SizedBox(
                  height: 40.0,
                ),
                RaisedButton(
                  onPressed: (){},
                  shape: StadiumBorder(),
                  color: Colors.blueAccent,
                  textColor: Colors.white,
                  child: Text("Login",style: TextStyle(fontSize: 22.0),),
                  padding: EdgeInsets.symmetric(vertical: 12.0,horizontal: 80.0),
                  elevation: 0.0,

                )
              ],
            ),
          )
        ],
      ),
    );
  }

}

```


