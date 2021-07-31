---
title: "《在Flutter中的Sliver2》"
subtitle: ""
date: 2021-07-06T20:23:51+08:00
lastmod: 2021-07-06T20:23:51+08:00
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



> 本文主要介绍

<!--more-->

们还会介绍SliverFixedExtentList以及SliverPrototypeExtentList这2枚用于固定元素尺寸的列表。视频最后还会介绍SliverFillViewport这枚类似于PageView的组件。

```
CustomScrollView(
        ///视窗 里面比外边大的一个东西哦哦、
        slivers: <Widget>[
          ///用于固定元素尺寸的列表
          SliverFixedExtentList(
            delegate: SliverChildListDelegate([
              FlutterLogo(),
              FlutterLogo(),
              FlutterLogo(),
            ]),
            itemExtent: 200,
          ),
          //转接器
          // SliverPrototypeExtentList(),
// SliverFillViewpor(),
        ],
      )
```



```
DefaultTextStyle(
        style: TextStyle(fontSize: 28,color: Colors.red),
              child: CustomScrollView(
          ///视窗 里面比外边大的一个东西哦哦、
          slivers: <Widget>[
            ///用于固定元素尺寸的列表
            SliverPrototypeExtentList(
              delegate: SliverChildListDelegate([
                Text("444"),
                Text("444"),
                Text("444"),
              ]),

              ///不会绘制到屏幕上，是一个原型
              prototypeItem: Container(height: 500,color: Colors.red,),
            ),
            //转接器
            
// SliverFillViewpor(),
          ],
        ),
      )
```



```
DefaultTextStyle(
        style: TextStyle(fontSize: 28, color: Colors.red),
        child: CustomScrollView(
          ///视窗 里面比外边大的一个东西哦哦、
          slivers: <Widget>[
            ///用于固定元素尺寸的列表
            SliverPrototypeExtentList(
              delegate: SliverChildListDelegate([
                Text("444"),
                Text("444"),
                Text("444"),
              ]),

              ///不会绘制到屏幕上，是一个原型
              prototypeItem: Container(
                height: 500,
                color: Colors.red,
              ),
            ),
            //转接器

            SliverFillViewport(
              ///填满整个视窗，相当于翻页
              delegate: SliverChildListDelegate([
                Container(color: Colors.red,),
                Container(color: Colors.black,),
                Container(color: Colors.blue,),
               
              ]),
            ),
          ],
        ),
      )
```



```
DefaultTextStyle(
        style: TextStyle(fontSize: 28, color: Colors.red),
        child: CustomScrollView(
          ///视窗 里面比外边大的一个东西哦哦、
          slivers: <Widget>[
            SliverToBoxAdapter(
              child: Placeholder(),
            ),
            SliverList(
                delegate: SliverChildListDelegate([
              FlutterLogo(
                size: 200,
              ),
              FlutterLogo(),
              FlutterLogo()
            ]))

            ///用于固定元素尺寸的列表
          ],
        ),
      )
```

