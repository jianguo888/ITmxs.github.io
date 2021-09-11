---
title: "查看nginx是否启动"
date: 2021-09-11T12:00:18+08:00
draft: false
tags: ["nginx"]
categories: ["nginx"]
---

> 本文主要介绍

# Linux如何查看Nginx是否启动

Nginx是一个高性能的反向代理服务器，现在一般作为我们网站或其他Web服务的第一层代理，它负责监听80端口，用户通过浏览器发送的请求首先经过的就是Nginx服务。如果Nginx没有启动或异常结束就会影响Web服务的正常使用。

那么如何在Linux中查看Nginx是否启动呢?

命令

**方法一：查看进程列表（条件过滤）**

Linux没运行一个程序就会产生一个进程，那么可以通过查看Nginx进程来判断它是否运行。

ps -ef |grepnginx

![img](https:////upload-images.jianshu.io/upload_images/17178902-296f523a2f2994e1.png?imageMogr2/auto-orient/strip|imageView2/2/w/771/format/webp)

**方法二：直接查看进程pid**

这种直接返回pid的方式比较适合跟其他程序结合使用，比如在shell/python脚本中执行这个命令拿到pid，让后根据pid来判断Nginx是否启动。

ps -C nginx -o pid

![img](https:////upload-images.jianshu.io/upload_images/17178902-a450485aa28b7f87.png?imageMogr2/auto-orient/strip|imageView2/2/w/425/format/webp)

**方法三：通过端口判断**

我们知道，Nginx监听的是80端口，那么就可以查看80端口运行的程序来判断Nginx是否运行

netstat -anp |grep:80

![img](https:////upload-images.jianshu.io/upload_images/17178902-a552ec723a63e5e0.png?imageMogr2/auto-orient/strip|imageView2/2/w/915/format/webp)

**方法四：使用lsof命令来查看80端口是否有程序在运行**

lsof-i:80

注意：此命令在CentOS下无法直接使用，需要使用yum install lsof安装

默认监听端口为80端口，如果修改了nginx的监听端口，查看80端口无法判断nginx是否运行。



作者：ming_Liu
链接：https://www.jianshu.com/p/47d00f759bd5
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。