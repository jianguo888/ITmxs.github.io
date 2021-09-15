---
title: "《Redis在windows下设置开机自启动》"
subtitle: ""
date: 2021-05-25T18:33:55+08:00
lastmod: 2021-05-25T18:33:55+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["redis"]
categories: ["redis"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/redis.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/redis.png"

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



启动命令

redis-server redis.windows.conf，出现下图显示表示启动成功了。

<img src="https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200630101141866.png" alt="image-20200630101141866" style="zoom:50%;" />

<img src="https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200630101016088.png" alt="image-20200630101016088" style="zoom:50%;" />

### 设置Redis服务使其开机自启

1、由于上面虽然启动了redis，但是只要一关闭cmd窗口，redis就会消失。所以要把redis设置成windows下的服务。

也就是设置到这里，首先发现是没用这个Redis服务的。

<img src="https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200630101045334.png" alt="image-20200630101045334" style="zoom:50%;" />

2、设置服务命令

```
redis-server --service-install redis.windows-service.conf --loglevel verbose
```

输入命令之后没有报错，表示成功了，刷新服务，会看到多了一个redis服务。

<img src="https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200630101118778.png" alt="image-20200630101118778" style="zoom:67%;" />

3、常用的redis服务命令。

卸载服务：redis-server --service-uninstall

开启服务：redis-server --service-start

停止服务：redis-server --service-stop

