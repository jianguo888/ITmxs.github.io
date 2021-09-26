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

# 启动

 启动代码格式：nginx安装目录地址 -c nginx配置文件地址

例如：

```
[root@LinuxServer sbin]# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```

 

# 停止

 nginx的停止有三种方式：

- ## 从容停止

　　1、查看进程号

```
[root@LinuxServer ~]# ps -ef|grep nginx
```

 

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102182744854-1291053517.png)

　　2、杀死进程

```
[root@LinuxServer ~]# kill -QUIT 2072
```

 

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102182652354-960281274.png)

- ## 快速停止

1、查看进程号

```
[root@LinuxServer ~]# ps -ef|grep nginx
```

 

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102183103651-1859453208.png)

2、杀死进程

```
[root@LinuxServer ~]# kill -TERM 2132
或 [root@LinuxServer ~]# kill -INT 2132
```

 

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102183340010-2024212451.png)

- ## 强制停止

 

```
[root@LinuxServer ~]# pkill -9 nginx
```

 

 

# 重启

## 1、验证nginx配置文件是否正确

### 方法一：进入nginx安装目录sbin下，输入命令./nginx -t

看到如下显示nginx.conf syntax is ok

nginx.conf test is successful

说明配置文件正确！

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102184633432-1268782338.png)

 

### 方法二：在启动命令-c前加-t

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102185023385-456612180.png)

##  2、重启Nginx服务

###  方法一：进入nginx可执行目录sbin下，输入命令**./nginx -s reload** 即可

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102185521057-1341380905.png)

### 方法二：查找当前nginx进程号，然后输入命令：kill -HUP 进程号 实现重启nginx服务

![img](https://images2015.cnblogs.com/blog/848552/201601/848552-20160102185838167-234856506.png)