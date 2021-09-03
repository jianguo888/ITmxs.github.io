---
title: "《Flutter文件下载器》"
subtitle: ""
date: 2021-06-01T13:54:15+08:00
lastmod: 2021-06-01T13:54:15+08:00
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



> 在这篇文章中，我们将构建一个简单的文件下载器，显示进度的视觉指示。

<!--more-->

------

## 创建用户界面

```dart
class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  var downloading = false;
  var done = false;
  double progress;

  @override
  Widget build(BuildContext context) => Scaffold(
    appBar: AppBar(
      backgroundColor: Colors.transparent,
      elevation: 0,
      centerTitle: true,
      title: Text('Downloader'),
      actions: [
        IconButton(
          icon: Icon(Icons.settings),
          onPressed: () {},
        )
      ],
    ),
    body: SizedBox.expand(child: Align(child: Column(
      mainAxisAlignment: MainAxisAlignment.center,
      crossAxisAlignment: CrossAxisAlignment.center,
      children: [
        if (downloading)
          SizedBox(
            width: 120,
            child: LinearProgressIndicator(
              value: progress,
            ),
          )
        else if (done) ...[
          Image.network('https://i.tst.sh/XaY4i.jpg'),
          Padding(
            padding: EdgeInsets.all(8),
            child: RaisedButton(
              child: Text('Reset'),
              onPressed: () {
                setState(() {
                  done = false;
                });
              },
            ),
          )
        ] else
          RaisedButton(
            child: Text('Start'),
            onPressed: () async {
              setState(() {
                downloading = true;
                progress = null;
              });

              for (int i = 0; i <= 100; i++) {
                setState(() {
                  progress = i / 100;
                });
                await Future.delayed(Duration(milliseconds: 25));
              }

              setState(() {
                downloading = false;
                done = true;
              });
            },
          ),
      ],
    ))),
  );
}
```

这只是一个wireframe，实际上并不下载数据。