---
title: "《Android系列3学习路线》"
subtitle: ""
date: 2021-07-30T23:27:02+08:00
lastmod: 2021-07-30T23:27:02+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["android"]
categories: ["android"]

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

### 一、前言

不知不觉自己已经做了几年开发了，由记得刚出来工作的时候感觉自己能牛逼，现在回想起来感觉好无知。懂的越多的时候你才会发现懂的越少。

如果你的知识是一个圆，当你的圆越大时，圆外面的世界也就越大。

最近看到很多Android新手问Android学习路线，学习方法啊，如何入门啊，所以我从网上找了一些资料，然后以我的工作经验给大家总结一下，让你们少走弯路，提取一些工作中经常用到的技术。

### 二、学习知识汇总

#### 1、Java se基础

> 1).Java基本数据类型与表达式，分支循环。
> 2).String和StringBuffer的使用、正则表达式。
> 3).面向对象的抽象，封装，继承，多态，类与对象，对象初始化和回收；构造函数、this关键字、方法和方法的参数传递过程、static关键字、内部类。
> 4).对象实例化过程、方法的覆盖、final关键字、抽象类、接口、继承的优点和缺点剖析；对象的多态性：子类和父类之间的转换、抽象类和接口在多态中的应用、多态带来的好处。
> 5).Java异常处理，异常的机制原理。
> 6).常用的设计模式：Singleton、Template、Strategy模式。
> 7).JavaAPI介绍：种基本数据类型包装类，System和Runtime类，Date和DateFomat类等。
> 8).Java集合介绍：Collection、Set、List、ArrayList、LinkedList、Hashset、Map、HashMap、Iterator等常用集合类API。
> 9).JavaI/O输入输出流：File和FileRandomAccess类，字节流InputStream和OutputStream，字符流Reader和Writer，以及相应实现类，IO性能分析，字节和字符的转化流，包装流的概念，以及常用包装类，计算机编码。
> 10).Java高级特性：反射和泛型。
> 11).多线程原理：如何在程序中创建多线程(Thread、Runnable)，线程安全问题，线程的同步，线程之间的通讯、死锁。

#### 2、android UI编程

1)、Android开发环境搭建：Android介绍，Android开发环境搭建，第一个Android应用程序，Android应用程序目录结构。

2)、Android初级控件的使用：

> TextView控件的使用
> Button控件的使用方法
> EditText控件的使用方法
> ImageView的使用方法
> RadioButton的使用方法
> Checkbox的使用方法
> Menu的使用方法

3)、Android高级控件的使用：

> ListView的使用方法
> GridView的使用方法
> Adapter的使用方法
> Spinner的使用方法
> Gallary的使用方法
> ScrollView的使用方法
> RecyclerView

4、对话框与菜单的使用：

> Dialog的基本概念
> BlockquoteAlertDialog的使用方法
> DatePickerDialog的使用方法
> Menu的使用方法
> 自定义Menu的实现方法

5.控件的布局方法：

> 线性布局的使用方法
> 相对布局的使用方法

6.多Acitivity管理：

> AndroidManifest.xml文件的作用
> Intent的使用方法
> 使用Intent传递数据的方法
> 启动Activity的方法
> IntentFilter的使用方法
> Activity Group的使用方法

7、自定义控件实现方法：

> 自定义ListView的实现方法
> 可折叠ListView的使用方法
> 自定义Adapter的实现方法
> 自定义View的实现方法
> 动态控件布局的上实现方法
> 上拉刷新下拉加载更多

#### 3、android网络编程与数据存储

1).基于Android平台的HTTP通讯：

> Http协议回顾
> 使用Get方法向服务器提交数据的方法
> 使用POST方法向服务器提交数据的实现方法
> 使用Http协议实现多线程下载
> 使用Http协议实现断点续传

2).Android数据存储技术：

> SQLite3数据库简介
> SQL语句回顾
> SQLite3编程接口介绍
> SQLite3事务管理
> SQLite3游标使用方法
> SQLite3性能分析
> 访问SDCard的方法
> 访问SharedPreferences的方法

参考自:http://www.cnblogs.com/zhuawang/p/3672228.html

### 三、进阶之路(初级->中级->高级)

#### 1、初级工程师

小明之前完全没接触过Android开发，是个应届生，他的待遇是13k，然后小明以校招的身份进入了百度，然后小明需要怎么学习才能成为初级工程师呢？这个时候，小明对编程基础、数据结构、C语言都有一定基础，Java语法什么的也都掌握的比较好，Android才有java语言，无奈的是小明并不会搞Android。

小明首先需要购买一本Android入门的书籍，为了更快地学习Android，小明业余时间也都用来一边看书一边照着书中的例子敲代码，结果2周时间小明就把这本书学了一遍。看完这本书后，小明对Android的历史、结构、代码规范等都有了一个大概的了解，并且，小明已经可以写出一些简单的Activity了。这个时候在小明眼里，Android开发很简单很好玩，通过在xml中摆放一些按钮文本框什么的就可以做一些界面了。

小明开始跟着他的技术导师做需求，一些简单的小需求小明自然是不在话下了。突然有一天来了一个需求，该需求要求小明在Activity中为一个button加一个动画效果，小明慌了：“完全没接触过，书上也没有讲，怎么办呢？”小明冷静了下，打开了百度搜索，输入“Android 动画”，打开前几个链接，小明恍然大悟，照着网上的例子把需求给实现了。后来导师告诉他：“学好Android，官方文档是必须看的，既全面又权威”。然后小明如获至宝，花了一年时间把上面的guide和training都看了一遍，并且他还动手抄了几个小例子。

有一天，小明又需要做一个动画相关的需求，这可难不倒小明，它熟练地打开了www.baidu.com，输入“Android 动画”，突然他楞了一下：”总不能每次写动画都要百度一下吧！“，于是他在CSDN开了一个博客，把动画相关的知识点都写上去，为的是后面再写动画相关的代码就不用百度去搜了，事实如何呢？后面再写动画相关的代码，小明的确不用再去百度搜了，因为通过写一篇动画博客，他把动画相关的细节都已经记住了，这样他就可以不用再去参考任何文档了，后来小明还学会了把一些琐碎的不方便放在博客上的东西写到了印象笔记上面，什么时候忘了10秒钟以内都可以快速找回来，而不是花10分钟去再次搜索一遍。

这里总结一下，Android入门的时候，需要有一本入门书，好好学习书中的内容，同时花一年时间把Android官方文档中的training和guide看一遍，同时通过写博客和记笔记的方式来做总结，建议让自己的每篇博客都有价值些。通过一年时间的学习，相信每个人都可以达到中级工程师的水平。

技术要求：

- 基本知识点
  比如四大组件如何使用、如何创建Service、如何进行布局、简单的自定义View、动画等常见技术
- 书籍推荐
  《第一行代码 Android》、《疯狂Android》

#### 2、中级工程师

小明经过一年的努力学习终于成为Android中级工程师了，月薪变成了17k。到了中级工程师，已经可以在公司里干很多体力活了，但是一些很重要的任务小明还不能一个人承担起来，这个时候小明需要学习的内容就很多了，如下所示：

- AIDL：熟悉AIDL，理解其工作原理，懂transact和onTransact的区别；
- Binder：从Java层大概理解Binder的工作原理，懂Parcel对象的使用；
- 多进程：熟练掌握多进程的运行机制，懂Messenger、Socket等；
- 事件分发：弹性滑动、滑动冲突等；
- 玩转View：View的绘制原理、各种自定义View；
- 动画系列：熟悉View动画和属性动画的不同点，懂属性动画的工作原理；
- 懂性能优化、熟悉mat等工具
- 懂点常见的设计模式

学习方法

阅读进阶书籍，阅读Android源码，阅读官方文档并尝试自己写相关的技术文章，需要有一定技术深度和自我思考。在这个阶段的学习过程中，有2个点是比较困扰大家的，一个是阅读源码，另一个是自定义View以及滑动冲突。

如何阅读源码呢？这是个头疼的问题，但是源码必须要读。阅读源码的时候不要深入代码细节不可自拔，要关注代码的流程并尽量挖掘出对应用层开发有用的结论。另外仔细阅读源码中对一个类或者方法的注释，在看不懂源码时，源码中的注释可以帮你更好地了解源码中的工作原理，这个过程虽然艰苦，但是别无他法。

如何玩转自定义View呢？我的建议是不要通过学习自定义view而学习自定义view。为什么这么说呢？因为自定义view的种类太多了，各式各样的绚丽的自定义效果，如何学的玩呢！我们要透过现象看本质，更多地去关注自定义view所需的知识点，这里做如下总结：

- 搞懂view的滑动原理
- 搞懂如何实现弹性滑动
- 搞懂view的滑动冲突
- 搞懂view的measure、layout和draw
- 然后再学习几个已有的自定义view的例子
- 最后就可以搞定自定义view了，所谓万变不离其宗

大概再需要1-2年时间，即可达到高级工程师的技术水平。我个人认为通过《Android开发艺术探索》和《Android群英传》可以缩短这个过程为0.5-1年。注意，达到高级工程师的技术水平不代表就可以立刻成为高级工程师（受机遇、是否跳槽的影响），但是技术达到了，成为高级工程师只是很简单的事。

技术要求：

- 稍微深入的知识点
  AIDL、Messenger、Binder、多进程、动画、滑动冲突、自定义View、消息队列等
- 书籍推荐
  《Android开发艺术探索》、《Android群英传》

#### 3、高级工程师

小明成为了梦寐以求的高级工程师，月薪达到了20k，还拿到了一丢丢股票。这个时候小明的Android水平已经不错了，但是小明的目标是资深工程师，小明听说资深工程师月薪可以达到30k+。

为了成为Android资深工程师，需要学习的东西就更多了，并且有些并不是那么具体了，如下所示：

- 继续加深理解”稍微深入的知识点“中所定义的内容
- 了解系统核心机制：

1. 了解SystemServer的启动过程
2. 了解主线程的消息循环模型
3. 了解AMS和PMS的工作原理
4. 能够回答问题”一个应用存在多少个Window？“
5. 了解四大组件的大概工作流程
6. …

- 基本知识点的细节

1. Activity的启动模式以及异常情况下不同Activity的表现
2. Service的onBind和onReBind的关联
3. onServiceDisconnected(ComponentName className)和binderDied()的区别
4. AsyncTask在不同版本上的表现细节
5. 线程池的细节和参数配置
6. …

- 熟悉设计模式，有架构意识学习方法

这个时候已经没有太具体的学习方法了，无非就是看书、看源码和做项目，平时多种总结，尽量将知识融会贯通从而形成一种体系化的感觉。同时这个阶段对架构是有一定要求的，架构是抽象的，但是设计模式是具体的，所以一定要加强下设计模式的学习。关于设计模式的学习，最近一本新书推荐给大家《Android 源码设计模式解析与实战》，既可以学习设计模式，又可能体会到Android源码中的设计思想，我最近也在阅读此书。

技术要求：

- 稍微深入的知识点
- 系统核心机制
- 基本知识点的细节
- 设计模式和架构
- 书籍推荐
  《Android开发艺术探索》、《Android 源码设计模式解析与实战》、《Android内核剖析》

参考自:http://blog.csdn.net/singwhatiwanna/article/details/49560409

### 四、学习视频跟书籍

当你有了学习线路，学习哪些内容，也知道以后的路怎么走了，理论看多了总要实践的，我这里给你们整理了大概80G的学习视频跟书籍，[赶快点我点我点我。。。下载。。](http://blog.csdn.net/lowprofile_coding/article/details/51059080)

如果你想第一时间看我们的后期文章，扫码关注公众号，每周不定期推送Android开发实战教程文章，你还等什么，赶快关注吧，学好技术，出任ceo，赢取白富美。。。。