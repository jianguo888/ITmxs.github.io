---
title: "《Flutter最佳实践整理3》"
subtitle: ""
date: 2021-07-30T22:37:36+08:00
lastmod: 2021-07-30T22:37:36+08:00
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



## 解决 setState() called after diapose()

网络请求成功前退出了页面，该 State 被从对象树卸载掉，而这时回调了网络请求的方法，方法中带有 setState 的调用，也就导致了该问题。

```
if (mounted) {
  setState(() {
    this._books = dataModel.books;
  });
}
```

## Waiting for another flutter command to release the startup lock…等待另一个flutter命令释放启动锁

1. 退出 VS Code。
2. 打开 flutter 安装目录 如：…\flutter\flutter\bin\cache 删除里面的 lockfile。
3. 重启打开VS Code。

原因：当你的项目异常关闭，下次启动就会出现上面的一行话，
此时需要打开 flutter/bin/cache/lockfile，删除就行了，
或者直接用下面的命令：rm ./flutter/bin/cache/lockfile。

## 在Stateless控件内部或者浮层内部刷新，可以使用StatefullBuilder

```
showDialog<void>(
  context: context,
  builder: (BuildContext context) {
    int selectedRadio = 0;
    return AlertDialog(
      content: StatefulBuilder(
        builder: (BuildContext context, StateSetter setState) {
          return Column(
            mainAxisSize: MainAxisSize.min,
            children: List<Widget>.generate(4, (int index) {
              return Radio<int>(
                value: index,
                groupValue: selectedRadio,
                onChanged: (int value) {
                  setState(() => selectedRadio = value);
                },
              );
            }),
          );
        },
      ),
    );
  },
);
```

这里通过selectedRadio变量记录Radio的是否选中的状态