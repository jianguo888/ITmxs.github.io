---
title: "《在Flutter中的Sliver》"
subtitle: ""
date: 2021-05-26T19:34:59+08:00
lastmod: 2021-05-26T19:34:59+08:00
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



```
CustomScrollView( ///视窗
        slivers: <Widget>[
          //转接器
SliverToBoxAdapter(child: FlutterLogo(
  size: 500,
),),SliverToBoxAdapter(child: FlutterLogo(
  size: 500,
),)


        
        ],
      )
```



```
 CustomScrollView(
        ///视窗
        slivers: <Widget>[
          //转接器

          SliverToBoxAdapter(
            child: Text("表头"),
          ),
          SliverToBoxAdapter(
            child: Text("表头2"),
          ),
          SliverToBoxAdapter(
            child: FlutterLogo(),
          ),
          SliverList(delegate: SliverChildBuilderDelegate((con, index) {
            return Container(
              height: 200,
              color: Colors.accents[index % 200],
            );
          }))
        ],
      )
```



```
CustomScrollView(
        ///视窗
        slivers: <Widget>[
          //转接器

          SliverToBoxAdapter(
            child: Text("表头"),
          ),
          SliverToBoxAdapter(
            child: Text("表头2"),
          ),
          SliverGrid(
              delegate: SliverChildBuilderDelegate((con, index) {
                return Container(
                  height: 200,
                  color: Colors.accents[index % 18],
                );
              },childCount: 23),
              gridDelegate:
                  SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 4)),
          SliverList(delegate: SliverChildBuilderDelegate((con, index) {
            return Container(
              height: 200,
              color: Colors.primaries[index % Colors.primaries.length],
            );
          }))
        ],
      )
```

