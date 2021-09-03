---
title: "《Flutter开发Tip3》"
subtitle: ""
date: 2021-07-30T22:48:30+08:00
lastmod: 2021-07-30T22:48:30+08:00
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



> 本文主要介绍flutter开发小技巧

<!--more-->

## 提高flutter attach的成功率

### 方案1

断开wifi，执行flutter attach，attach成功后再链接wifi

### 方案2

通过指定设备uuid进行attach

1. 通过`flutter devices`查找已链接设备列表，获取链接设备的uuid
2. `flutter attach -d <#uuid#>` 指定想要attach的设备进行连接，例如`flutter attach -d AE5D772C-6D56-43AD-83F2-0554257B16C4`

[![Xnip2020-09-17_02-35-23](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_attach.jpg)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_attach.jpg)

### 方案3

通过指定应用的app-id进行attach

1. 同一wifi环境下如果有多台相同项目设备连接中，找到你需要attach的app-id进行连接
2. 执行：`flutter attach --app-id <#app_id#>`例如：`flutter attach --app-id com.xxx.xxx`

## 从package中加载json文件

### 背景

例如我这里有一个flutter的demo项目叫`flutter_demo_package_json_load`，在根目录通过命令：`flutter create --template=package json_test_package`,创建了一个名为`json_test_package`的package，在package的assets中有一个为test2.json文件，现在需要在package中加载到改文件。

### 从工程加载json

对于普通项目的json文件，加载方式如下：

1. 在项目中添加json文件，例如项目的assets下有一个名为test1.json的文件

2. 我们在yaml中添加如下依赖

   ```
   # 只加载assets下的test1.json文件
   assets:
     - assets/test1.json
   
   # 或者
   # 加载assets下的所有文件
    assets:
     - assets/
   ```

3. 通过rootBundle加载

   ```
   Future<String> _loadFromProjectAsset() async {
     // json file in project,is ok
     return rootBundle.loadString("assets/test1.json");
   }
   ```

但是对于package下面json文件上面的方法失效了，使用上面的方法加载将会报错，错误信息如下：

[![image-20200917144141986](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_15.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_15.png)

错误原因是资源路径不对，加载不到，因为rootBundle类没有提供package name的参数，我们只能把package name信息放在资源路径里面。

### 从package加载json

灵感来自https://pub.dev/packages/lottie库的`Lottie.asset(“assetsPath”,package: “package_name”)`方法，因为lottie实现动画的原理也是加载一份json配置，不过这个方法支持指定package配置。

[![image-20200917145538741](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_16.png)](https://blog.wangruofeng007.com/images/flutter_tips_s2/flutter_tips_16.png)

这里的keyName就是我们rootBundle类里面需要用的资源路径，包含带package的情况。

正确的方式如下：

1. 第一步和上面一样，将json文件添加到工程中，假如这里我们package里面的json文件名为test2.json

2. 在yaml中添加如下依赖

   ```
   # 只加载assets下的test2.json文件
   assets:
    - assets/test2.json
   
   # 或者
   # 加载assets下的所有文件
   assets:
    - assets/
   ```

3. 通过rootBundle加载，但是path的格式为 `packages/<#package_name#>/<#file_path#>`

这里为

```
// json file in package,this is work
Future<String> _loadFromPackageAsset() async {
  // 【importance】 packag json file need use this formate:
  // `packages/$package/$assetName`
  return rootBundle.loadString("packages/json_test_package/assets/test2.json");
}
```

通过`DefaultAssetBundle`也是一样可以

```
// This is work too.
Future<String> testLoadJsonFileByDefaultAssetBundle(BuildContext context) {
  // 【importance】 packag json file need use this formate:
  // `packages/$package/$assetName`
  return DefaultAssetBundle.of(context)
      .loadString('packages/json_test_package/assets/test2.json');
}
```

> [Demo链接](https://github.com/wangruofeng/flutter_demo_package_json_load)