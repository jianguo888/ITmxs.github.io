---
title: "《SqlServer服务远程调用失败》"
subtitle: ""
date: 2021-05-27T07:09:54+08:00
lastmod: 2021-05-27T07:09:54+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: []
categories: []

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: ""
featuredImagePreview: ""

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

参考链接   https://blog.csdn.net/u010850027/article/details/43410137

由于开发系统，需要vs版本统一，于是经过了昨天一整天艰苦卓绝的斗争，小编终于成功的写在了13版本的vs，重新装上了12版本的vs，本来想着，12版本的vs搭建成功了，就赶快搭建框架，然后敲例子，可是天意弄人，小编的数据库神奇般的打不开了，又经过半天的百度，问题答案渐渐浮出水面，接下来小编就简单的介绍一下如何解决Sql Server服务远程过程调用失败的问题，打开sql，连接上，出现一下问题：

第一种方法，如下所示

这个问题，小编肿么有种似曾相识的赶脚nie，原来早在2014年9月1号的时候，小编已经和这个问题有过一面之缘，详情请见链接在与SQL Server建立连接时出现与网络相关的或特定于实例的错误，于是小编就循着上次解决方法的脚步，开始解决问题，可是天意弄人，虽然出了同样的错误，可是不一定是同样的原因所导致，于是小编就开始了一段百度之路。接下来，小编就来和大家分享一下如何解决Sql Server服务远程过程调用失败的解决方法，小编拖着腮帮想，可能是这台电脑之前安装了 visual studio 2013，内置的SQl server 2012与之冲突，导致新的2008 R2 版本过低导致。出现如下图所示的错误：




      奇怪，之前所有的服务都不见了，被妖怪抓走了，开玩笑`(*∩_∩*)′，通过网上查，才知道是因为SQL Server2008 与VS2012 共存的问题，当你电脑中同时装了这两样东西，SQL 就会出现上图中的情况。解决办法：从控制面板或者其他辅助软件中找到”Microsoft SQL Server Expres 2012LocalDB“将其卸载即可，如下图所示：


​       

       卸载之后，重新刷新一下，“SQL Server服务”就出出来，如下图，然后再按照下图中的第二个红线框将已停止的SQL Server(MSSQLSERVER)启动即可。如下图所示：


​         

         到此为止，很多小伙伴的问题应该能够解决，可能有些小伙伴由于RP的原因，当启动SQL Server（MSSQLSERVER）之后又出现了新的问题。具体如下图： 


​         

         通过查看windows日志文件（查看日志文件的方法）就能看出是因为端口被占用的原因，解决这个问题也挺容易的，只需要将SQLEXPRESS服务中后面三个都关掉，保证每次启动只有一个启动就好。问题终于解决了。如下所示：


​         

        而后，打开数据库，就可以连上了哦，出了上面的解决方法之外，小逼还向小伙伴简单介绍两种解决方法：



        第二种办法就是升级数据库
使用更高版本的数据库，或者升级SqlServer2008为SP1或者SP2。

        第三种方法就是手动启动数据库服务项
        方法如下：
       右击“计算机”→“管理”→“服务”，找到SQL Server（MSSQLSERVER），右击，选择“启动”。此时登陆数据库不会有问题，但是重新启动系统后，问题会依旧存在的。



         小编寄语：该博文，小编主要简单的介绍了一下关于sql server服务远程过程调用失败问题的解决方法，终于明白，世界上最远的距离不是生与死，而是你坐在电脑面前却连不上数据库，出现问题，并不可怕，可怕的是我们被看似简单的东西我们的心，可能解决一个简单的问题会花上我们一个半天的时间，但是问题解决了的那种欣喜和高兴，比小时候吃大白兔奶糖还要开心，项目开发，未完待续......







错误日志：



SQL Server 无法生成 FRunCM 线程。请查看 SQL Server 错误日志和 Windows 事件日志，获取有关可能发生的相关问题的信息。

由于网络库中存在内部错误，所以无法启动网络库。要确定原因，请查看错误日志中紧位于此错误之前的那些错误。

TDSSNIClient 初始化失败，出现错误 0x7e，状态代码 0x1。原因: 初始化失败，出现基础结构错误。请检查以前的错误。 找不到指定的模块。

TDSSNIClient 初始化失败，出现错误 0x7e，状态代码 0x60。原因: 无法初始化 VIA 侦听器。 找不到指定的模块。

SQL Server 无法初始化 VIA 支持库 [QLVipl.dll]。这通常指示 VIA 支持库不存在或已损坏。请修复或禁用 VIA 网络协议。错误: 0x7e。



网络转载：

我们可以判定，可能会是以下问题，- 别处理如下： 

1、可能是IP地址配置不对。解决方法- 下： 

打开 Microsoft SQL Server 2005配置工具下的SQL Server Configuration Manager，选择mssqlserver协议, 然后双击右边窗口的TCP/IP，在弹出窗- 中检查IP配置。 

一般来讲，默认的IP2是：127.0.0.1，如- 第一个IP地址的端口1433端口ping不通的- 话，服务无法启动。您可以将127.0.0.1- 在IP1上，那么不管IP2的端口是否能ping- 通，服务都能够正常启动。您也可以- IP1设置为当前机器的IP地址，并保证- IP地址的端口能够ping通。 

2、可能是因为VIA协议启用造成的。解- 方法如下： 

打开 Microsoft SQL Server 2005配置工具下的SQL Server Configuration Manager，选择mssqlserver协议, 然后右边窗口有个VIA，点击右键禁用- ，点击“确定”即可。这时候再启动s- qlserver服务即可,如果还不行,可以重新启动下电脑,我的就是禁用后还是不行,电脑重新启动后能用了！ 

另外，管理员密码修改也会造成sqlserve- r服务无法启动。解决方法如下： 

打开 Microsoft SQL Server 2005配置工具下的SQL Server Configuration Manager，打开SQL Server属性， 

将本帐户的密码修改为您更改之后的- 理员密码，“确定”即可。这时SQL Server服务应该就能正常启动了。



我的是VIA没有应用导致 ，禁用之后可以。但是由于机器名字改过，现在登陆不是，下续……