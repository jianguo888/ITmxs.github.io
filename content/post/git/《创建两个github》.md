---
title: "《创建两个github》"
subtitle: ""
date: 2021-08-01T09:35:32+08:00
lastmod: 2021-08-01T09:35:32+08:00
draft: false
author: ""
authorLink: ""
description: ""

tags: []
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false

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
---

<!--more-->



- 打开Windows设置 ->应用->可选功能，然后添加OpenSSH服务器和OpenSSH客户端。

![image-20210801093611351](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210801093611351.png)

- ## 资源管理器打开-计算机管理器

  使用快捷键: win + R 打开资源管理器, 输入: **compmgmt.msc** 点击: 确定



点开服务



![14d674fc5c9be9475158117ed940f464.png](https://img-blog.csdnimg.cn/img_convert/14d674fc5c9be9475158117ed940f464.png)

- 打开openSSH Authentication 功能和 openSSH SSH Server功能

鼠标右键，点击启动。



![e8fc930fc9cb52ac101bf202a9f89402.png](https://img-blog.csdnimg.cn/img_convert/e8fc930fc9cb52ac101bf202a9f89402.png)

### 5.打开 ssh-agent

在gitbash中输入：

```
eval `ssh-agent -s`
```

注意，`ssh-agent` 被 ```` 包围

### 6. 清除已经存在的认证



![6f04690eac9afa2d0b7f62d690320087.png](https://img-blog.csdnimg.cn/img_convert/6f04690eac9afa2d0b7f62d690320087.png)

**这一步不是必须的**

如果你是一个在遇到这篇教程之前已经看了其他教程并增加过新的keys的同学，我建议你把之前手动增加的keys清除了(没有成功吗不是？)

删除所有 *手动* 添加的键：在gitbash中输入：

```
ssh-add -D
```

但是你如果没有不愉快的过往，那么就忽略这一步吧。

### 7.增加新的keys



![2716196bfb1f1c3b5073825ab0fa41a0.png](https://img-blog.csdnimg.cn/img_convert/2716196bfb1f1c3b5073825ab0fa41a0.png)

在gitbash中输入：

```
ssh-add id_rsa_one



ssh-add id_rsa_two
```

### 好啦！配置完毕！