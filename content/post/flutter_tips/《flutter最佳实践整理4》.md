---
title: "《Flutter最佳实践整理4》"
subtitle: ""
date: 2021-07-30T22:38:23+08:00
lastmod: 2021-07-30T22:38:23+08:00
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

## 平台相关的判断

只关心是否是iOS和Android的情况下不需要依赖`context`，优先使用`Platform`

```
Platform.isAndroid
Platform.isIOS
```

需要详细知道具体哪个平台才使用`TargetPlatform`

这个API的缺点是需要依赖`context`这个参数

```
final platform = Theme.of(context).platform;

if (platform == TargetPlatform.android) {
	...
} else if (platform == TargetPlatform.iOS) {
	...
}
```

## ScrollView 滑动隐藏键盘

https://stackoverflow.com/questions/55306855/hide-keyboard-on-scroll-in-flutter

使用ScrollView的`keyboardDismissBehavior`属性

```
ListView(
	keyboardDismissBehavior: ScrollViewKeyboardDismissBehavior.onDrag
)
```

## 回调写法

### a.无参数回调

VoidCallback

### b.有一个参数回调

ValueChanged

### c.参数大于一个

可以通过`typedef`自定义一个函数

下面是Example用法

```
final VoidCallback onPressed;
final ValueChanged<T> onSelectHandler;
typedef ImageSwiperOnTap = void Function(int index, List<String> imgUrls);
```

## flutter pub get is stuck

可以通过切换flutter镜像到中文站点来解决

使用系统shell，请编辑

使用oh_my_zsh, 需要编辑`.zshrc`

```
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

保存文件后，关闭，下次重新打开终端生效

再执行`flutter pub get` 查看速度是否正常