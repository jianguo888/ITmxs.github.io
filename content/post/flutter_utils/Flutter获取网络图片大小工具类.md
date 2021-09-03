---
title: "Flutter获取网络图片大小工具类"
date: 2021-08-18T11:17:54+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---



```
import 'package:flutter/cupertino.dart';

Size getNetImageSize(String url) {
  int startIndex = url.lastIndexOf("_") + 1;
  if (startIndex == 0 || startIndex >= url.length) {
    return Size(0, 0);
  }
  int endIndex = url.lastIndexOf(".");
  if(endIndex == -1)
  {
    return Size(0, 0);
  }
  String sizeStr =
      url.substring(url.lastIndexOf("_") + 1, url.lastIndexOf("."));
  List<String> size = sizeStr.split("x");
  if(size.length < 2)
  {
    return Size(0, 0);
  }
  int w = int.tryParse(size[0]);
  int h = int.tryParse(size[1]);
  w ??= 0;
  h ??= 0;
  return Size(w.toDouble(), h.toDouble());
}

```





```
    Size size = getNetImageSize(widget.datas.picUrl[0]);
```

