---
title: "《在Flutter中的多版本控制》"
subtitle: ""
date: 2021-02-26T21:44:35+08:00
lastmod: 2021-02-26T21:44:35+08:00
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



## 1先安装  choco

powershell 管理员身份运行 不会的话，先打开一个powershell 窗口 ，然后右击任务栏，选择第一个，即可进入

![image-20210726214610912](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210726214610912.png)

输入命令

```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```



可以安装成功

![image-20210726214718072](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210726214718072.png)



接下来安装fvm

```
 choco install fvm
```

![image-20210726215007597](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210726215007597.png)

## 2fvm 常用 命令命令

##### 2.1 查看已安装的 Flutter 版本

```
fvm list
```

##### 2.2 全局设置版本的 Flutter

```
fvm global 2.2.0
```

##### 2.3 单个项目使用某个版本的 Flutter

```
fvm use 2.2.0
```

##### 2.4 移除某个版本的 Flutter

```
fvm remove 2.2.0
```

## 3设置 IDE

现在我们来看看如何配置 IDE，下面我们展示了如何在 android studio 和 VS Code 中进行配置，现在让我们来看看。

- Android Studio

在根项目目录中复制下面的绝对符号链接。

```
Example: /absolute/path-to-your-project/.fvm/flutter_sdk
```

然后我们将在 Android Studio 的菜单中打开 Languages and Frameworks-> Now search for flutter or flutter and change the path to flutter SDK。然后实施改变。现在您可以使用选定的 Flutter 版本运行它并调试它。如果你想看到新的设置，然后我们可以使用 Android 工作室将重新启动。

![image-20210726215356076](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210726215356076.png)

- VS Code

现在我们将在这里配置 VS Code，我们将看到如何完成 VS Code 过程。

目录的路径，我们可以在代码中看到 FVM 安装的所有版本

```
"dart.flutterSdkPaths": ["$YOUR_PATH/fvm/versions",],
```

为了获得上面的路径，我们将执行 fvm list 命令

```
// copy this path
Versions path:  $YOUR_PATH/fvm/versions
```

输入 cmd + shift + p 来使用 sdk，然后输入 change sdk，现在你可以选择你喜欢的版本了。

![image-20210726215452423](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210726215452423.png)

## 总结

在这篇文章中，我对版本管理做了一个简单的解释，你可以根据自己的需要对其进行修改和实验，这个简单的介绍来自于版本管理的 Flutter。

我希望这个博客将提供您尝试在 Flutter 版本管理充分的信息。我们向您展示了 Flutter 探索版本管理和工作在您的 Flutter 应用程序，所以请尝试它。