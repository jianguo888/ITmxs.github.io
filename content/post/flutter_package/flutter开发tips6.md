---
title: "flutter开发tips1"
date: 2021-08-18T14:05:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]
---



# Responsive Framework



[responsive_framework](https://pub.dev/packages/responsive_framework) 

# Flexfold

 [demo phase](https://rydmike.com/demoflexfold/#/)

# Flutter Screenutil

# Device Info Plus

这是一个包，顾名思义，为您提供**信息** **有关** **的** **设备**。这是查询用户使用的设备然后基于该设备显示小部件的一种非常好的方法。原来的包 device_info 已经被这个替换/改进了，所以旧包不再带来更新，而是新包[device_info_plus](https://pub.dev/packages/device_info_plus)。因为它是由 Flutter 社区开发的，所以会有一致的更新，不再有更新的可能性非常低。

# Flutter Neumorphic

# Styled Widget

使用此[包，](https://pub.dev/packages/styled_widget)您可以**更轻松地**设计和**构建小部件树**。从示例中可以看出，您可以非常轻松地创建非常出色的 UI。

# Getwidget

[Getwidget](https://getwidget.dev/) Getwidget是Flutter 的开源UI 库，它带有 1000 多个预构建的 UI 组件。您可以立即在那里创建和尝试许多小部件。

# Logger

每个人都**讨厌** **冗长的、难以理解的错误消息**，然后你会永远搜索错误，对吗？而这个包正好解决了这个问题。有了这个，你会得到如此**清晰的错误信息**，这是必须的！

# Faker

# Cached Network Image

在应用程序中，您经常使用来自互联网的图像，这很好，但它们总是会重新加载。这个包通过在第一次加载后缓存图像来解决这个问题，因此不必一遍又一遍地重新加载！

```
CachedNetworkImage(
	imageUrl: "<http://via.placeholder.com/350x150>",
	placeholder:`(context, url) => CircularProgressIndicator(),
	errorWidget: (context, url, error) => Icon(Icons.error),
),
```

