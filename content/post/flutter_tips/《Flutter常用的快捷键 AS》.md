---
title: "《Flutter常用的快捷键 AS》"
subtitle: ""
date: 2021-08-20T10:47:09+08:00
lastmod: 2021-08-20T10:47:09+08:00
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



> 本文主要介绍flutter中的开发技巧1

<!--more-->

# Flutter常用的快捷键 AS（Android Studio一下简称AS）

## 代码块

### StatelessWidget和StatefulWidget







快速创建基于StatelessWidget和StatefulWidget的小部件，这两组代码块是最常见的了。

只需要输入 stless 就可以创建一个StatelessWidget。

![img](https://i2.wp.com/img-blog.csdnimg.cn/20200607195827436.png)

只需要输入 stful 就可以创建一个StatefulWidget。

![img](https://i2.wp.com/img-blog.csdnimg.cn/20200607195900757.png)

### 自定义代码块

AS中可以设置代码块。Settings -> Editor -> Live Templates







我们可以参考自带的slful代码块进行设置。点击??的 + 就可添加代码块了。

![img](https://i2.wp.com/img-blog.csdnimg.cn/20200607195950645.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pbmdpb3M=,size_16,color_FFFFFF,t_70)

## 快捷键

Ctrl + option + O 删除未使用的import

option + Enter 自动import未导入的文件

如果你有一个文件的import被删除了，直接对报错的类名称使用option + Enter搞定导入的动作。







![img](https://i2.wp.com/img-blog.csdnimg.cn/20200607200023556.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pbmdpb3M=,size_16,color_FFFFFF,t_70)

如果你不想每次创建类都导入文件，那么我们可以直接根据提示创建对象。将类名输入，然后AS就不需要你手动import了。

![img](https://i2.wp.com/img-blog.csdnimg.cn/20200607200118441.png)

Shift + F6 重命名（如果是Touch Bar就是Shift + Rename...）

CMD + -/+ 折起/展开代码块

CMD + . 折起/展开选中代码

CMD + , 进入设置页面

CMD + [ 光标回到上一次编辑的位置







CMD + ] 光标回到下一次编辑的位置

CMD + L 定位某一行，甚至某一个字符

![img](https://i2.wp.com/img-blog.csdnimg.cn/20200607200237426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pbmdpb3M=,size_16,color_FFFFFF,t_70)

CMD + / 注释

CMD + Y 查看选中类的属性

CMD + O 快速打开（一般用于快速打开某个文件）

option + Enter 扩展功能(很重要！)

查看小部件源码

- CMD + Click(鼠标左键)
- CMD + B
- CMD + Down（小键盘↓）
- F4（非Touch Bar键盘）

Option + up(↑) 选中上一层代码（比如选中当前光标的单词，选中当前小部件的所有代码，自己试~）

CMD + option + M 将选中代码提取到某个方法中

CMD + option + L 格式化代码

CMD + Shift + -/+ 折起/展开所有代码块

Option + Shift + Up/Down 上下移动行

Command + Shift + Up/Down 上下移动方法

自动格式化代码 （这是一个AS的设置）

Settings -> Editor -> Languages & Frameworks -> Flutter -> Editor

选中Format code on save 也可以勾选子选项 Organize imports on save

注意：我这里使用的是V 3.6.3 可能将来会有变化。







![img](https://i2.wp.com/img-blog.csdnimg.cn/2020060720041673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pbmdpb3M=,size_16,color_FFFFFF,t_70)