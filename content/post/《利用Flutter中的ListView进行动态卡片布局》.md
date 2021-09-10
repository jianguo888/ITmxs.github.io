---
title: "《利用Flutter中的ListView进行动态卡片布局》"
subtitle: ""
date: 2021-04-30T12:05:08+08:00
lastmod: 2021-04-30T12:05:08+08:00
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



> 本文主要介绍 listView下进行动态卡片布局

<!--more-->

真香！本来觉得用以前的java就能做的为啥还用flutter，但是最近接触flutter之后感觉这才是写移动应用的神器啊！
尤其是用java写的listView，各种适配器传参简直了

## 效果

可以显示gif动图哦，真强大，某些模拟器中可能有闪烁现象，但是我的手机上没有，可能是兼容问题 )
![img](https://uploadfiles.nowcoder.com/images/20200417/2967044_1587086827827_F3E1A26286A1814C96AE9A791F8885FB)

## 原理

在完成这个效果之前，
分两步走，listview列表和card布局

listView

![image-20210731120806213](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210731120806213.png)



没有适配器，没有viewHolder,没有entity，flutter将他变成了一个纯前端的项目,简单到任何的注释都是那么的苍白

```
return ListView(
    children: <Widget>[
      Card(
        margin: EdgeInsets.all(10),
        child: Column(
          children: <Widget>[
            ListTile(
              title: Text('张三',
                  style: TextStyle(color: Colors.red),
                  textDirection: TextDirection.ltr),
              subtitle: Text('工程师'),
            )
          ],
        ),
      ),
      Card(
        margin: EdgeInsets.all(10),
        child: Column(
          children: <Widget>[
            ListTile(
              title: Text('李四',
                  style: TextStyle(color: Colors.red),
                  textDirection: TextDirection.ltr),
              subtitle: Text('产品经理'),
            )
          ],
        ),
      ),
      Card(
        margin: EdgeInsets.all(10),
        child: Column(
          children: <Widget>[
            ListTile(
              title: Text('王五',
                  style: TextStyle(color: Colors.red),
                  textDirection: TextDirection.ltr),
              subtitle: Text('程序员'),
            )
          ],
        ),
      )
]
); //

```

当然以上代码要写在build函数中

### card



![image-20210731120723721](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210731120723721.png)
加了注释，应该能看懂

```
return ListView(
    children: <Widget>[
      Card(
        margin: EdgeInsets.all(10),//表示边界距离
        child: Column(
          children: <Widget>[
            AspectRatio(
              aspectRatio: 16/9, //调整宽高比
              child:Image.network("http://img1.izaoxing.com/allimg/c190122/154Q51262OG0-c029.jpg",fit: BoxFit.cover,)//图像铺满
            ),
            ListTile(
              leading: CircleAvatar(  //专门将图片转换为头像
                backgroundImage:NetworkImage("http://img1.izaoxing.com/allimg/c190122/154Q51262OG0-c029.jpg")
              ) ,
              title: Text('张三',
                  style: TextStyle(color: Colors.red),
                  textDirection: TextDirection.ltr),
              subtitle: Text('工程师'),
            )
          ],
        ),
      ),

]
); //

```

## 合并

难点在于，不能像一开始的listView那样单纯的重复Card,我们需要传参
同时，内容与代码必须要分离
**以下代码没有删减，可直接复制使用**

在lib中新建一个资源包，新建文件
![img](https://uploadfiles.nowcoder.com/images/20200417/2967044_1587086828500_76D7AA0BFE3E095AD4A16DEEAFF1FE4C)
写入以下代码，实现逻辑与内容的分离

```
List listData=[
  {
    "title":'first',
    "imageUrl":'http://img1.izaoxing.com/allimg/c190122/154Q51262OG0-c029.jpg',
    "description": 'the beautiful'
  },
  {
    "title":'second',
    "imageUrl":'https://pic2.zhimg.com/80/v2-40c024ce464642fcab3bbf1b0a233174_hd.jpg',
    "description": 'the beautiful'
  },
  {
    "title":'third',
    "imageUrl":'https://pic2.zhimg.com/v2-848ed6d4e1c845b128d2ec719a39b275_b.jpg',
    "description": 'the beautiful'
  },
  {
    "title":'fourth',
    "imageUrl":'https://pic1.zhimg.com/50/v2-88b5da103cb64fe9e3f0d7b9d33fcfa4_hd.webp',
    "description": 'the beautiful'
  }
];

```

在main.dart中，我们传入以下代码(import的路径可能需要修改)
将listData通过map形式传入成为value,然后取相应的key值传入对应的参数，listView会自动循环遍历value中的内容

```
import 'package:flutter/material.dart';
import 'package:flutter_app/res/listData.dart';
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return MaterialApp(
      home: Scaffold(
          appBar: AppBar(
            title: Text("flutterCard"),
          ),
          body: LayoutDemo()),
    );
  }
}

class LayoutDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    //TODO: implement build
    return ListView(
        children: listData.map((value){
          return Card(
            margin: EdgeInsets.all(10),
            child: Column(
              children: <Widget>[
                AspectRatio(
                  aspectRatio: 16/9,
                  child: Image.network(value["imageUrl"],fit: BoxFit.fill,),
                ),
                ListTile(
                  leading: CircleAvatar(
                    backgroundImage: NetworkImage(value["imageUrl"]),
                  ),
                  title:Text(value["title"]),
                  subtitle: Text(value["description"])
                )
             ],
          )
          );
        }).toList(),//注意这里要转换成列表，因为listView只接受列表
    );
  }
}


```

综上，只需两个文件，几十行代码即可完成布局，开心O(∩_∩)O~~