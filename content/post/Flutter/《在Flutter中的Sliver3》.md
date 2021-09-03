---
title: "《在Flutter中的Sliver3》"
subtitle: ""
date: 2021-06-06T21:17:15+08:00
lastmod: 2021-06-06T21:17:15+08:00
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



> 本文主要介绍

<!--more-->

```
 DefaultTextStyle(
        style: TextStyle(fontSize: 28, color: Colors.red),
        child: CustomScrollView(
          ///视窗 里面比外边大的一个东西哦哦、
          slivers: <Widget>[
            SliverAppBar(
              floating: true, //往下拉时第一时间显示标题 配合snap使用
              // pinned: true, //一直显示
              snap: true, //轻轻一拉就都显示
              stretch: true, //允许向下拉伸
              title: Text("标题"),
              expandedHeight: 200,
              flexibleSpace: FlexibleSpaceBar(
                background: FlutterLogo(),
                title: Text("FlexibleSpaceBar标题"),
                stretchModes: [
                  StretchMode.blurBackground, //模糊背景
                  StretchMode.fadeTitle, StretchMode.zoomBackground,

                  //下拉杉，拉伸的时候出现的效果
                ],
                collapseMode: CollapseMode.parallax, //缩回去的时候 的效果
              ),
            ),
            SliverToBoxAdapter(
              child: Placeholder(),
            ),

            SliverList(
                delegate: SliverChildListDelegate([
              FlutterLogo(
                size: 200,
              ),
              FlutterLogo(
                size: 600,
              ),
              FlutterLogo()
            ]))

            ///用于固定元素尺寸的列表
          ],
        ),
      )
```





```
  SliverOpacity(
              opacity: 0.9,
              sliver: SliverToBoxAdapter(
                child: FlutterLogo(
                  size: 500,
                ),
              ),
            ),
```





```
 SliverAnimatedOpacity(
              duration: Duration(
                seconds: 100,
              ),
              opacity: 0.9,
              sliver: SliverToBoxAdapter(
                child: FlutterLogo(
                  size: 500,
                ),
              ),
            ),
```



```
SliverIgnorePointer(
              ignoring: false,
              sliver: SliverToBoxAdapter(
                child: FlatButton(
                  onPressed: () {},
                  child: FlutterLogo(
                    size: 500,
                  ),
                ),
              ),
            ),
```



```
 SliverList(
                delegate: SliverChildListDelegate([
              FlutterLogo(
                size: 200,
              ),
              FlutterLogo(
                size: 600,
              ),
              FlutterLogo()
            ])),
```



```
SliverGrid(
              delegate: SliverChildListDelegate([
                Icon(
                  Icons.ac_unit,
                ),
                Icon(
                  Icons.ac_unit,
                ),
                Icon(
                  Icons.ac_unit,
                ),
              ]),
              gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
                maxCrossAxisExtent: 80,
              ),
            ),
```





```
SliverPadding(
              padding: EdgeInsets.all(10),
              sliver: SliverIgnorePointer(
                ignoring: false,
                sliver: SliverToBoxAdapter(
                  child: FlatButton(
                    onPressed: () {},
                    child: FlutterLogo(
                      size: 500,
                    ),
                  ),
                ),
              ),
            ),
```



```
 SliverToBoxAdapter(
              child: Divider(),
            ),
            SliverToBoxAdapter(),
```





```
   //当前视窗
            SliverFillRemaining(
              child: Placeholder(),
            )
```



```
SliverFillRemaining(
              child: Center(
                child: CircularProgressIndicator(),
              ),
            )
            
            
            

```







```
 SliverLayoutBuilder(
              builder: (context, constraints) {
                print(constraints);
                return SliverToBoxAdapter();
              },
            )
```





```
 SliverConstraints(
              
            ),            ),
```

