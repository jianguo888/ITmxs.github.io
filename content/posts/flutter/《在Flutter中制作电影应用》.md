---
title: "《在Flutter中制作电影应用》"
subtitle: ""
date: 2021-07-16T20:36:33+08:00
lastmod: 2021-07-16T20:36:33+08:00
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



> 本文主要介绍一个使用 Flutter 的酷电影应用的简单 UI 设计

<!--more-->

```dart
import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(
  home: MovieApp(),
));

class MovieApp extends StatefulWidget {
  @override
  _MovieAppState createState() => _MovieAppState();
}

class _MovieAppState extends State<MovieApp> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xff1C262F),
      appBar: AppBar(
        elevation: 0.0,
        backgroundColor: Color(0xFF1B2C3B),
        title: Text("Movie App"),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.more_vert, color: Colors.white,),
          )
        ],
      ),
      //Creating the Chip list
      body:Padding(
        padding: const EdgeInsets.only(top: 12.0),
        child: ListView(
          children: <Widget>[
           Container(
             height: 40.0,
             width: double.infinity,
             child: ListView(
               scrollDirection: Axis.horizontal,

               children: <Widget>[
                 Padding(
                   padding: EdgeInsets.symmetric(horizontal: 12.0),
                   child: Chip(
                     label: Text("All",style: TextStyle(color: Colors.white)),
                     backgroundColor: Colors.orange,
                     padding: EdgeInsets.only(left: 12.0, right: 12.0),
                   ),
                 ),
                 Padding(
                   padding: EdgeInsets.symmetric(horizontal: 12.0),
                   child: Chip(
                     label: Text("Action",style: TextStyle(color: Colors.white),),
                     backgroundColor: Colors.blueGrey,
                     padding: EdgeInsets.only(left: 12.0, right: 12.0),
                   ),
                 ),
                 Padding(
                   padding: EdgeInsets.symmetric(horizontal: 12.0),
                   child: Chip(
                     label: Text("Adventure",style: TextStyle(color: Colors.white)),
                     backgroundColor: Colors.blueGrey,
                     padding: EdgeInsets.only(left: 12.0, right: 12.0),
                   ),
                 ),
                 Padding(
                   padding: EdgeInsets.symmetric(horizontal: 12.0),
                   child: Chip(
                     label: Text("Comedie",style: TextStyle(color: Colors.white)),
                     backgroundColor: Colors.blueGrey,
                     padding: EdgeInsets.only(left: 12.0, right: 12.0),
                   ),
                 ),
               ],
             ),
           ),
            SizedBox(height: 12.0,),
            Row(
              children: <Widget>[
                Padding(
                  padding: const EdgeInsets.only(left: 12.0),
                  child: Text(
                    "Top Trends",
                    style: TextStyle(
                        color: Colors.orange,
                        fontSize: 26.0,
                        fontWeight: FontWeight.bold
                    ),
                    textAlign: TextAlign.start,
                  ),
                ),
              ],
            ),
            SizedBox(height: 12.0,),
            Container(
              width:double.infinity,
              height: 230.0,
              child: ListView(
                scrollDirection: Axis.horizontal,
              children: <Widget>[
                Padding(
                  padding: const EdgeInsets.symmetric(horizontal:12.0),
                  child: MovieCard("JOKER", "8.5/10","assets/joker.png"),
                ),
                Padding(
                  padding: const EdgeInsets.symmetric(horizontal:12.0),
                  child: MovieCard("Avengers", "8.5/10","assets/avengers.png"),
                ),
                Padding(
                  padding: const EdgeInsets.symmetric(horizontal:12.0),
                  child: MovieCard("Terminator", "8.5/10","assets/terminator.png"),
                ),
              ],
              ),
            ),
            SizedBox(height: 25.0,),
            Row(
              children: <Widget>[
                Padding(
                  padding: const EdgeInsets.only(left: 12.0),
                  child: Text(
                    "Top Trends",
                    style: TextStyle(
                        color: Colors.orange,
                        fontSize: 26.0,
                        fontWeight: FontWeight.bold
                    ),
                    textAlign: TextAlign.start,
                  ),
                ),
              ],
            ),
            SizedBox(height: 12.0,),
            Container(
              width:double.infinity,
              height: 230.0,
              child: ListView(
                scrollDirection: Axis.horizontal,
                children: <Widget>[
                  Padding(
                    padding: const EdgeInsets.symmetric(horizontal:12.0),
                    child: MovieCard("JOKER", "7.5/10","assets/joker.png"),
                  ),
                  Padding(
                    padding: const EdgeInsets.symmetric(horizontal:12.0),
                    child: MovieCard("Interstellar", "9.5/10","assets/interstellar.png"),
                  ),
                  Padding(
                    padding: const EdgeInsets.symmetric(horizontal:12.0),
                    child: MovieCard("Terminator", "8.5/10","assets/terminator.png"),
                  ),
                  SizedBox(height: 22.0,)
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
  
  Widget MovieCard(String movieName,String rate,String imgPath){
    return InkWell(
      onTap: (){},
      child: Column(
        children: <Widget>[
          Card(
            elevation: 0.0,
            child:Image.asset(imgPath,fit: BoxFit.fill,width: 130.0,height: 160.0,),
          ),
          SizedBox(height: 5.0,),
          Text(movieName,
            style: TextStyle(
              color: Colors.white,
              fontWeight: FontWeight.bold,
              fontSize: 26.0
            ),
            textAlign: TextAlign.start,
          ),
          SizedBox(height:5.0),
          Text(rate,style: TextStyle(
            color: Colors.white,
            fontSize: 18.0
          ),),
        ],
      ),
    );
  }
}
```


