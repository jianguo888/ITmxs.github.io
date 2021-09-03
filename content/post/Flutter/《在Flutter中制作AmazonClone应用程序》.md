---
title: "《在Flutter中制作AmazonClone应用程序》"
subtitle: ""
date: 2021-03-17T21:24:12+08:00
lastmod: 2021-03-17T21:24:12+08:00
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



> 本文主要介绍如何制作这个简单但漂亮的 Amazon Clone 应用程序 U

<!--more-->

在本教程中，我将向您展示如何制作这个简单但漂亮的 Amazon Clone 应用程序 UI，当然，我们不会构建一个具有后端服务、支付方式和所有这些东西的完整应用程序。我们将只关注 UI。我们将看到如何创建滚动视图，如何制作底部导航栏，以及如何使用 flutter 创建自定义小部件。





源代码：

```dart
import 'dart:async';

import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    debugShowCheckedModeBanner: false,
    home: AmazonApp(),
  ));
}

class AmazonApp extends StatefulWidget {
  @override
  _AmazonAppState createState() => _AmazonAppState();
}

class _AmazonAppState extends State<AmazonApp> {
  //Here I'm going to create a custom widget for the items box
  Widget Items(String name, String category, String imgUrl, String price) {
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: InkWell(
        // this widget will make your container clickable
        onTap: () {},
        child: Container(
          width: 180.0,
          decoration: BoxDecoration(
            color: Colors.white,
            borderRadius: BorderRadius.circular(8.0),
          ),
          child: Column(
            children: [
              Image(
                image: NetworkImage(imgUrl),
                width: 170.0,
                height: 160.0,
                alignment: Alignment.center,
              ),
              Text(
                name,
                style: TextStyle(fontSize: 22.0),
              ),
              Text(
                category,
                style: TextStyle(
                  fontSize: 18.0,
                  color: Colors.grey,
                ),
              ),
              Text(
                "$price£",
                style: TextStyle(
                    fontSize: 22.0,
                    color: Color(
                      0xFFFF9900,
                    )),
              ),
            ],
          ),
        ),
      ),
    );
  }

  //Now I'm going to import a list of images that I collected from Unsplash and amazon website
  //you can choose the image that you want and add them into the list
  List<String> ImgUrl = [
    "https://in.jbl.com/on/demandware.static/-/Sites-masterCatalog_Harman/default/dw4a6e76c7/C150SI-black1-hero-1605x1605px.png",
    "https://purepng.com/public/uploads/large/samsung-galaxy-s10-prism-front-6bx.png",

 
    "https://www.mypinnacleview.com/wp-content/uploads/2017/07/macbook-pro-png.png",

 
    "https://static.bhphoto.com/images/images2500x2500/1574693242_1297189.jpg",
    "https://images-na.ssl-images-amazon.com/images/I/61fTX5TjAEL._UX569_.jpg",
    "https://images-eu.ssl-images-amazon.com/images/G/31/img17/PC/sprocket/Tile-2-1._SS200_.jpg",
    "https://images-eu.ssl-images-amazon.com/images/I/51JKnkxCl7L._AC_SX184_.jpg",
    "https://images-na.ssl-images-amazon.com/images/I/710tUwxHq7L._UL1500_.jpg",
    "https://images-na.ssl-images-amazon.com/images/I/61LQQSc9fZL._UL1440_.jpg",
  ];
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xFF10161D),
      appBar: AppBar(
        backgroundColor: Color(0xFF222e3D),
        title: Text(
          "Amazon",
          style: TextStyle(
            color: Color(0xFFFF9900),
          ),
        ),
        centerTitle: true,
        actions: [
          FlatButton(
            onssed: () {},
            child: Icon(
              Icons.shopping_cart,
              color: Colors.white,
            ),
          )
        ],
      ),
      body: SingleChildScrollView(
        child: Container(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            mainAxisAlignment: MainAxisAlignment.start,
            children: [
              Padding(
                padding: EdgeInsets.symmetric(horizontal: 38, vertical: 20.0),
                child: TextField(
                  decoration: InputDecoration(
                    contentPadding: EdgeInsets.all(20.0),
                    focusedBorder: OutlineInputBorder(
                      borderRadius: BorderRadius.circular(4.0),
                      borderSide: BorderSide(color: Colors.transparent),
                    ),
                    prefixIcon: Icon(
                      Icons.search,
                      color: Colors.black,
                    ),
                    hintText: "Search...",
                    fillColor: Colors.white,
                    filled: true,
                  ),
                ),
              ),
              Padding(
                padding: const EdgeInsets.all(10.0),
                child: Text(
                  "New Items",
                  style: TextStyle(
                    color: Colors.white,
                    fontSize: 28.0,
                  ),
                ),
              ),
              //Now we will add the items list
              Container(
                width: double
                    .infinity, // this line will make the container take the full width of the device
                height:
                    270.0, // when you want to create a list view you should precise the height and width of it's container
                child: ListView(
                  scrollDirection: Axis
                      .horizontal, // this will make the list scroll horizontally
                  children: [
                    //now let's add an item to test
                    Items("JBL headphones", "Audio", ImgUrl[0], "63.3"),
                    Items("Sumsung", "Smartphones", ImgUrl[1], "633.3"),

 
                    Items("MacBook pro", "Tech", ImgUrl[2], "1200.56"),
                    Items("Blue yeti", "Audio", ImgUrl[3], "123.3"),
                    //Note that this is the UI of the App but you can easly implement a backend service via a REST API
                  ],
                ),
              ),
              //for the second part of the app it's the same principale so let's just copy the first elements
              //Let's add some space
              SizedBox(
                height: 20.0,
              ),

              Padding(
                padding: const EdgeInsets.all(10.0),
                child: Text(
                  "Recommended for you",
                  style: TextStyle(
                    color: Colors.white,
                    fontSize: 28.0,
                  ),
                ),
              ),
              Container(
                width: double
                    .infinity, // this line will make the container take the full width of the device
                height:
                    270.0, // when you want to create a list view you should precise the height and width of it's container
                child: ListView(
                  scrollDirection: Axis
                      .horizontal, // this will make the list scroll horizontally
                  children: [
                    //now let's add an item to test
                    Items("Jacket", "Fashion", ImgUrl[4], "63.3"),
                    Items("IMac Pro", "Tech", ImgUrl[5], "6353.63"),
                    Items("Books", "Books", ImgUrl[6], "12.56"),
                    Items("Sunglasses", "Fashion", ImgUrl[7], "6.3"),
                    Items("Watch", "Fashion", ImgUrl[8], "600.9"),
                    //Note that this is the UI of the App but you can easly implement a backend service via a REST API
                  ],
                ),
              ),
            ],
          ),
        ),
      ),

      //Now i'm going to add a bottom navbar
      bottomNavigationBar: BottomNavigationBar(
        backgroundColor: Color(0xFF10161d),
        selectedItemColor: Color(0xFFFF9900),
        unselectedItemColor: Colors.white,
        items: [
          //you should at least have 2 items with the icon and title or you will have an error
          BottomNavigationBarItem(
            icon: Icon(Icons.home),
            title: Text("Home"),
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.favorite),
            title: Text("Favorite"),
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.person),
            title: Text("Account"),
          ),
        ],
        //that's it for the UI, thank you for watching, if you want to see a full Tutorial
        //on how to build a fully functional shopping app, just put it in the comment
        //Don't forget to subscribe ;) <3
      ),
    );
  }
}

```

