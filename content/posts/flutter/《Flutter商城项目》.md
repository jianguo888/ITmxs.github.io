---
title: "《Flutter商城项目01》"
subtitle: ""
date: 2021-03-13T17:48:45+08:00
lastmod: 2021-03-13T17:48:45+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","项目"]
categories: ["flutter","项目"]

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
comment:
  enable: true
mapbox:
  accessToken: ""
---



> 项目描述：最早的京东商城是一个 b2c 商城 , 现在的京东商城是一个 b2c2b 的商城。我们这里给大家仿照的是以前的 b2c 版本的京东商城。

<!--more-->



![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/clip_image002.jpg)



使用到的插件有：

```yaml
  cupertino_icons: ^1.0.0
# 权限请求
  permission_handler: ^5.0.1+1

  #App版本信息
  package_info: ^0.4.3+2
  #用来加载网络数据
  dio: ^3.0.10
  # WebView
  webview_flutter: ^1.0.7

  #偏好设置
  shared_preferences: ^0.5.12+4

  # 饼图
  flutter_echart: ^1.0.2
    #path: /Volumes/code/ico/Desktop/code/flutter_app/flutter_echart

  # 路径获取
  path_provider: ^1.6.24
  #视频播放
  video_player: ^1.0.1
  #toast 消息提示小弹框
  fluttertoast: ^7.0.1+1
  #打开第三方地图
  map_launcher: ^1.1.2 
  flutter_svg: ^0.18.0
  #版本更新
  flutter_xupdate: ^1.0.2 
  #手机通讯录
  # easy_contact_picker: ^0.0.2 
  #分享
  share: ^0.6.5+4
  #图片选择，相机或者相册
  image_picker: ^0.6.7+4
  #图片裁剪
  image_cropper: ^1.3.1
  #Dart常用工具类库。包含日期，正则，倒计时，定时任务，时间轴等工具类
  flustars: ^0.3.3
    # 轮播组件
  flutter_swiper: ^1.1.6
    #  下拉刷新组件
  # pull_to_refresh: ^1.6.4
  cached_network_image: ^2.5.0
  azlistview: ^1.1.0
  lpinyin: ^1.1.0
  #生成二维码的库，可增加logo
  qr_flutter: ^3.2.0
  # 保存图片用到的库
  image_gallery_saver: ^1.6.7
#Flutter抖动动画组件，FLutter颤动动画
  shake_animation_widget: ^2.1.2
  flutter_bloc: ^6.1.1
#极光推送
  jpush_flutter: ^0.6.3
#异步通信
  provider: ^4.3.3
  #国际化
  flutter_localizations:
    sdk: flutter 
    #指纹生物
  local_auth: ^0.6.3+4
  #直播
  flutter_ijkplayer: ^0.3.5+1
  flutter_alipay: ^1.0.0
  #第三方应用
  url_launcher: ^5.7.10
  sy_flutter_wechat: ^0.2.2
  toast: ^0.1.5
  date_format: ^1.0.8
```

